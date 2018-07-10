---
layout: default
title: Modules
permalink: /modules/
tags: ['basics']
---
### xGraph Modules

An xGraph **module** is a collection of entities that work together to perform a service or task. Each module 
includes:
1. at least one entity to act as the Apex
2. the module structure, in a schema.json file
3. a module.json file that describes the modules interface
4. a README.md file for user documentation and a README.html file for developer documentation

First we will explain how to use a module in an xGraph system. Next, we will look at the parts of a module in 
detail.

#### Using A Module
A module can be included in a system in the system's structure, or generated later using the `genModule` 
function. Module instances that are instantiated when the system is run are included in the `Modules` system, 
while modules that will be included later using `genModule` can be pre-built by adding it to the the system's 
structure object under the `Deferred` section. 

When the system is run, modules listed in the `Modules` section of the system's structure object will be 
built and saved in a system's cache, and then instantiated as a specific instances. This means that the 
modules is loaded into memory, assigned a Pid, and sent the `Setup` and `Start` commands as necessary.

A module's definition is an object with three references: `Module`, `Source`, and `Par`. `Module` references 
the module to be loaded. `Source` references a path to a local source or module broker. `Par` references an 
object holding parameters as key-value pairs that will be passed to the module. Here is an example of a 
module definition.

```
{
    Module: "xGraph.Popup",
    Source: "core",
    Par: {
        Left: "com.mouse.x",
        Top: "com.mouse.y",
        View: "xGraph.3DView",
        Width: 800,
        Height: 600
    }
}
```

Here is the system structure object for the Plexus system in the MultipleSystemWithPlexus example. This 
system generates two modules on instantiation.
```
{
    "Sources": {
        "core": "{core}"
    },
    "Modules": {
        "Plexus": {
            "Module": "xGraph.Plexus",
            "Source": "core",
            "Par": {}
        },
        "Proxy": {
            "Module": "xGraph.Proxy",
            "Source": "core",
            "Par": {
                "Link": "$Plexus",
                "Role": "Server"
            }
        }
    }
}
```

The modules listed in the `Deferred` section of the system's structure object will be pre-loaded in the 
system's cache, so that when they are later generated using `genModule` the module is already built. This 
reduced the amount of latency when generating a module, because modules that are not listed in the system's 
structure object will have to be built before they can be generated and added to a system. 

Modules that are not included in the `Deferred` section of the system's structure object will have to be 
built before they can be generated, and the system must be run with the --allow-add-module flag to allow the 
system to generate an unknown module. 

`Deferred` modules are listed with their `Module` namespace and a path to their local or module broker 
`Source`, as seen in the system structure object for a Validate module system.
```
{
    "Sources": {
        "core": "{core}"
    },
    "Modules": {
        "Server": {
            "Module": "xGraph.Validate",
            "Source": "core",
            "Par": {
                "TestModule": "xGraph.SQLite",
                "TestJson": "@file: ../../Core/Modules/xGraph/SQLite/test.json"
            }
        },
        "Deferred": [
            {
                "Module": "xGraph.SQLite",
                "Source": "core"
            }
        ]
    }
}
```

The parameter object, `Par`, will be built and included in the module definition object sent to the 
`genModule` function. Here you can see the Validate module generating the module that is being tested using 
the information passed through Validate's `Par`.
```
    //build the module instance from test.json required state
    let instance = {};
    instance.Module = that.Par.TestModule;
    instance.Par = that.Vlt.Test.State;
	
    //instantiate the module
    //this calls setup and start in the instance if necissary
    log.v("Calling genMod on ", JSON.stringify(inst, null, 2));
    that.genModule(instance, callback);

    function callback(err, instApex) {
        that.Vlt.InstModule = instApex;
        that.Vlt.Test = setPid(that.Vlt.Test);
        log.v("Test Json is :", JSON.stringify(that.Vlt.Test, null, 2));
        if (typeof document != "undefined") {

            instance.Module = "xGraph.RootView";
            instance.Par = {
                 Layout: instApex
            };
            that.genModule(inst, (err, instApex) => {
                RunTests();
            });
        } else {
            RunTests();
        }
    }
```


#### Apex Entity
The module's **apex** is the entity that will handle incoming messages. This entity is identified as the apex 
in the module's structure. 

#### Other Entities
An **entity** is the lowest fractal layer in an xGraph system. All entities must be encapsulated in a module 
to be run in a system. Inside of a module, each entity is contained in a single JavaScript file. 

Each entity must be defined in the module's structure, in the `schema.json` file. This tells xGraph how the 
entities within a module can communicate with each other. Entities are discussed in greater detail in the 
[Entity Guide](1.4-Entity-Guide). Next, we will look at the module's structure and the `schema.json` file.

#### Module Structure (schema.json)
The module's structure, defined by an object in the `schema.json` file, describes the internal architecture 
of a module. In the `schema.json` file, apex entity is defined first, followed by any other entities in the 
module. 

Each entity is declared with a reference key in the module's structure object. Then, other entities can 
reference an entity as a parameter by prefixing the key with a hashtag. All entities will have a reference to 
the Apex entity. A simple example where the apex entity references the `"OtherEntity"` is found below.

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

#### Module Interface (module.json)
The `"module.json"` file determines the **module's interface**. This object has several keys. The first set 
of keys reference information about the module's identity. The next set keys reference the input and output 
commands that the module can send and receive.

The first part of the object includes the following keys.
- `"name"`, the module's name.
- `"version"`, the module's version number, which may include decimals.
- `"doc"`, the .md file that has the module's user documentation (see module documentation guide for details 
   on documenting your module).
- `"info"`, an object that can hold additional information as key-value pairs, including the modules 
  `"author"`. Any useful information can be included here.

The next key, `"input"`, holds information about the input commands the module can receive. This key holds an 
object with two references, `"required"` and `"opts"`. Both keys reference arrays of objects that represent 
commands the module can receive. Commands listed under `"required"` are required for the module to work 
properly, while commands listed under `"optional"` are optional. 

Each input command object has three keys; `"Cmd"` references the command name, and `"required"` and `"opts"` 
reference objects that hold arguments that will be passed with the command. Arguments referenced in 
`"required"` must be provided, and arguments referenced in `"opts"` are optional.

The next key, `"output"`, holds information about the output commands the module can send. This key holds an 
object with two references, `"required"` and `"optional"`. Both keys reference arrays of objects that 
represent commands the module can send. Commands listed under `"required"` are required for the module to 
work properly, while commands listed under `"optional"` are optional. 

Each output command object has four keys; `"Cmd"` references the command name, "par" is only used by an 
xGraph system and references the Pid of a module, and `"required"` and `"optional"` reference objects that 
hold arguments that will be passed with the command. Arguments referenced in `"required"` must be provided, 
and arguments referenced in `"optional"` are optional.

Below is an example of a simple module.json file.
```
{  
  "name": "Ping",
  "version": "1.0",
  "doc": "README.md",
  "info": {
    "author": "Introspective Systems"
  },
  "input": {
    "required":[
      {
        "Cmd": "",
        "required": {
        },
        "optional": {
        }
      }
    ],
    "optional":[
      {
        "Cmd": "Pong",
        "required": {
        },
        "optional": {
        }
      }
    ]
  },
  "output":{
    "required":[
      {
        "par":"",
        "Cmd": "",
        "required": {
        },
        "optional": {
        }
      }
    ],
    "optional":[
      {
        "par":"",
        "Cmd": "Ping",
        "required": {
        },
        "optional": {
        }
      }
    ]
  },
  "par": {
    "required": {},
    "optional": {}
  }
}
```

#### Documentation (README.md and README.html)
Each module must include both user and developer documentation. A brief description of these files is found 
below. For more information about module documentation, see the 
[Module Documentation Requirements](3.1-Module-Documentation-Requirements).

The README.md file holds user documentation. This is where things like examples and tutorials can be added. 

The README.html file is mainly used for developer documentation. This file should include information for 
each entity in a module, and can be generated using doxdox document generator. For information on how to 
generate documentation, see the [Documentation Generation Guide](3.2-Documentation-Generation-Guide).
