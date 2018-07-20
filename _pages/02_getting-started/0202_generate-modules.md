---
layout: default
title: Generating Modules
permalink: /generating-modules/
tags: ['getting-started']
---

# Generating Modules

xGraph fosters an open source community and we encourage developers to build their own xGraph modules. 
Additionally, if you are building a specialized system, you may want to build your own xGraph modules. 

It is easy to start building an xGraph module using the xgraph Executable Command Line Interface (CLI). 

To generate a new module with the name `ModuleName` use the following command:
```
xgraph generate module ModuleName
```

This command will generate a new module, with the name `ModuleName`. If you provide the generate module 
command a path instead of a simple string, the module will be generated at the path provided.

The directory `ModuleName` will have a `ModuleName.js` entity file, a module structure object in a 
schema.json file, and a module interface object in a module.json file. 

The  `ModuleName.js` file is the modules Apex entity. The generated entity looks like the entity below:
```
//# sourceURL=ModuleName.js
(function ModuleName() {
	class ModuleName {
		Setup(com, fun) {
			//this function is typically used to allow the entity/module to handle any internal setup
			//procedures prior to being connected to by other entities/modules

			fun(null, com);
		}

		Start(com, fun){
			//this function is typically used to allow the entity/module to handle any external setup
			//procedures

			fun(null, com);
		}
	}
	return {dispatch:ModuleName.prototype}
})();
```

This generated entity is a JavaScript class. It has two commands, `Setup` and `Start`. Like all xGraph 
commands, `Setup` and `Start` accept two arguments: `com`, the command object, and `fun`, the callback 
function.

The command object can pass arguments with the command through its key-value pairs. Every command object has 
they `"Cmd"` key, valued with the command name. Additional arguments can use any key.

The callback function must be called when the command has completed. The callback function should only be 
called once for each call to the command. 

Code that is added to `Setup` and `Start` can be called by Nexus when the module is generated. You can add 
more commands to your xGraph entity, which must be called by some entity, or another module. 

You can add more entities to the module by adding JavaScript entity files and adding the entity to the 
modules structure (schema.json file).

The schema.json file holds the modules structure. The module's structure describes how the entities within 
the module work together. After generation, the schema.json file will look like:
```
{
	"Apex": {
		"$Setup": "Setup",
		"$Start": "Start",
		"Entity": "ModuleName.js"
	}
}
```

To add another entity to the module, you have to add the entity to the modules structure, as follows: 
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
You will notice `"Apex"` has key `"Friend"` that references `"#OtherEntity"`. The `#` reference will be 
replaced by the Pid of OtherEntity, allowing the Apex to communicate with OtherEntity. 

The module.json file is the module's interface file. This is where you will add information on how to 
communicate with the module. This information includes the module's definition parameters, input commands 
that the module can receive, and output commands that the module can send. 

The only commands that need to be included in the the module interface are the commands that the module 
receives from other modules, or commands that the module sends to other modules. Commands that are sent 
between entities within the same module do not need to be documented here. 

Additionally, some basic information about the modules identity is recorded in the module's interface file. 
The following is an example of a blank `module.json` file.
```
{
	"name": "",
	"version": "",
	"info": {
		"author": ""
	},
	"doc": "README.md",
	"input": {
		"required": [
			{
				"Cmd": "",
				"required": {},
				"optional": {}
			}
		],
		"optional": [
			{
				"Cmd": "",
				"required": {},
				"optional": {}
			}
		]
	},
	"output": {
		"required": [
			{
				"par": "",
				"Cmd": "",
				"required": {},
				"optional": {}
			}
		],
		"optional": [
			{
				"par": "",
				"Cmd": "",
				"required": {},
				"optional": {}
			}
		]
	},
	"par": {
		"required": {},
		"optional": {}
	}
}
```
