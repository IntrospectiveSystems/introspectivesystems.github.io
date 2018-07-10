---
layout: default
title: Entities
permalink: /entities/
tags: ['basics']
---
### A Guide to the xGraph Entity

An **entity** is the base component (function and data) of all xGraph solutions. An entity is a single 
JavaScript function that is instantiated with the xGraph core. An entity must be encapsulated in a module to 
be run in an xGraph system.  An entity is generated using an entity definition. If the entity is generated 
when the module is generated, the entity's definition is found in the module's `"schema.json"` file. If the 
entity is generated dynamically by another entity in the module, the entity definition is found in the entity 
that called the generation.

#### Entity Definition
Below is an example of an entity definition.
```
"HelloWorld2": {
    "Entity": "HelloWorld2.js",
    "SomePar": "SomeValue"
}
```

The entity definition starts by declaring the entity file with the `"Entity"` key. After the entity file is 
listed, you can add any number of entity parameters as key-value pairs. 

The `"Entity"` key defines the JavaScript file that has the entities function.

The `"SomePar"` key is an example of a parameter that will be passed to the entity as a key, value pair. This 
parameter can be accessed in the context of the entity by accessing `this.Par.SomePar`. Any number of 
parameters can be added here. 


#### JavaScript Function
An entity is represented by a single JavaScript class or function in a single JavaScript file. 

The most simple way to create a JavaScript entity is with a JavaScript class. xGraph can interpret a 
JavaScript file with a single JavaScript class as an entity. 

Additionally, an entity can be a self-executing function that returns a dispatch table. xGraph can interpret 
a JavaScript file with a single JavaScript self-executing function as an entity. To use a self-executing 
function as an entity, the function must return the entities dispatch table. This is an object containing the 
entity's commands. 
```
   var dispatch = {
        Start: Start,
        Pong:Pong
   };
```

If the self-executing function contains a JavaScript class, you can return the class prototype as the 
dispatch table. 

	return {
		dispatch: LayoutManager.prototype
	}

#### Commands
Most of the entities code consists of command functions. Both types of entity will have a number of 
functions, called xGraph commands. These functions accept two arguments: the command object, `com`, and a 
callback function, `fun`. Each function must be called by an xGraph command.

`Start` and `Setup` are commands that xGraph can send to modules on instantiation. These functions can be 
included in the module's apex entity but are not required. Then when they are sent to a module, `Setup` and 
`Start` commands are received by the module's apex entity. 

To tell a module how it should handle the `Setup` and `Start` commands, you must include the `"$Setup"` and 
`"$Start"` keys in the entity definition of the module's apex entity, pointing to the function that should 
respond to the command. 
```
{
	"Apex": {
		"$Setup": "Setup",
		"$Start": "Start",
		"Entity": "HelloWorld.js",
		"Friend": "#OtherEntity"
	},
	"OtherEntity": {
		"$Start": "Start",
		"Entity": "HelloWorld2.js"
	}
}
```

Here is an example of a simple `Setup` command.
```
   function Setup(com,fun) {
        console.log("--HelloWorld/Setup");

        // Nothing happens here, continue on...

        if(this.Par.SomePar) {
            console.log("There is a parameter SomePar valued: " + this.Par.SomePar);
        }

        if (fun){
            fun(null, com);
        }
    }
```
Finally, here is an example of a simple, yet complete, entity.
```
//# sourceURL=Ping
(
    /**
     * The Ping entity communicates with the Pong entities.
     * Starting with the Ping module, the Start() function calls Pong.
     * Encapsulating module is defined in the PingPong system config file.
     * @param {pid} this.Par.Pong   The Pid of the Pong entity.
     */
function Ping() {

    /*
        Define all commands. This will be returned so that the xGraph core knows what
        commands the entity can accept.
    */
    var dispatch = {
        Start: Start,
        Pong:Pong
    };


    //-------------------------------------------------------Start
    /**
     * The Start command sets up a setTimeout function that will send the first "Ping" command
     * to the entity referenced in this.Par.Pong.
     *
     * @param {object} com  The command object.
     * @callback fun
     */
    function Start(com,fun) {
        console.log("--Ping/Start");

        // Launch the ball by sending the first Ping a command to Pong
        // This command does not need any information back, so it is sent
        //  without a callback
        this.send({"Cmd":"Ping"}, this.Par.Pong);


        if (fun){
            fun(null, com);
        }
    }

    //-------------------------------------------------------Pong
    /**
     * The Pong command is sent from the Pong entity, Pong.js. When Pong is received,
     * this module prints "Pong" in the console, then sends a 'Ping' command to the
     * module referenced in this.Par.Pong.
     * @param {object} com  The command object.
     * @callback fun
     */
    function Pong(com, fun){
        console.log("          |      Pong");
        // some useful variables
        let that = this;
        let Par = this.Par;

        // Wait one second before sending Ping command back.
        // This prevents stack overflow due to infinent loop.
        setTimeout(sendCommand, 1000);

        function sendCommand() {
            // Send Ping command to the Pong Module. Like before, this command does not need
            //      any information back, so it is sent without a callback
            // Here you will see we have used the 'that' variable to retain the
            //      parent functions context.
            that.send({"Cmd": "Ping"}, Par.Pong);
        }

        // call the Callback
        if (fun){
            fun(null, com);
        }
    }

    // return the dispatch, the commands that this entity can receive.
    return {
        dispatch: dispatch
    };
})();
```