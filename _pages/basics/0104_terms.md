---
layout: default
title: Terms and Definitions
permalink: /terms/
tags: ['basics']
---

This document is an introduction to the terminology we will be using while talking about xGraph systems. The following definitions describe components of xGraph systems, as well as supportive definitions that help us talk about what is happening in an xGraph system. So, let's just jump into it.

## The Definitions

### Entity 
An entity is the base component (function and data) of all xGraph solutions. An entity is a single JavaScript function that is instantiated with the xGraph core. An entity must be encapsulated in a module to be run in an xGraph system.  There is a base definition of the Entity class in the Nexus.js code. All entities can be uniquely described by their pid. For details on xGraph entities, see our [Entity Guide](1.4-Entity-Guide).

### Pid
A 32 character, globally unique ID. Every entity has a **pid** and this pid is unique.

### Par
The `this.Par` object of an entity that is defined during instantiation of that entity. These can be set in code, the module's schema.json or the system's config.json. Notably, this is where the entities pid can be found (`this.Par.Pid`). The **Par** object can be saved by the `this.save()` function into the cache, thus making it persistent. 

### Vault
This `this.Vlt` object of an entity that is defined during instatiation of that entity. Data found in this object is temporal and is deleted when the system stops.

### Module 
A **Module** is a collection of entities that work together to perform a service/task. The structure is defined by a schema.json file. Each **module** has a single entity that serves as the Apex of the module. For details on xGraph modules, see our [Module Guide](1.3-Module-Guide).

### Module Type (or Template, or Class)
The module definition separate from any instantiations of the module. 

### Apex
The particular entity that was defined by the **Apex** key in the schema.json of the module to be the input and output manager of the module. This means that all messages sent to this module are handled in this entity, and all messages sent from this module are sent from this entity. Only the **Apex** has the capability of sending and receiving messages outside the context of the module.

### Module Structure (schema.json)
The template for building a module.  Here is a basic example:

```
{
	"Apex": {
		"$Setup": "Setup",
		"$Start": "Start",
		"Entity": "HelloWorld.js",        
		"Friend": "#Entity1"
	},
	"Entity1": {
		"$Setup": "Setup",
		"$Start": "Start",
		"Entity": "HelloWorld2.js"
	}
}

```

### System 
A collection of one or more Modules that are instantiated by a single kernel process (Nexus.js in node). For details on xGraph systems, see our [Systems Guide](1.2-Systems-Guide).

### System Structure Object
The template for building a system, often found in a config.json file.

```
{
	"Sources": {
		"xGraph": "{xGraph}"
	},
	"Modules": {
		"HelloWorld":{
			"Module": "xGraph.HelloWorld",
			"Source": "xGraph",
			"Par":{
				"SomePar": "Hello Mars"
			}
		}
	}
}
```

### Nexus
The kernel process that starts and gives function to the xGraph system. Nexus can use either a system structure object or a cache to start a system.

### Cache
The directory where a completely encapsulated system exists once it is instantiated. The **cache** consists of a directory for every module type in the system. Each module type directory contains a module.json description of the module, as well as a directory for each instance of the module. Finally, each module instance directory contains a JSON description of the module instance.

### Project (or Galaxy, or System of Systems) 
A **project** is a system or a collection of systems that interact to perform a task.

### Proxy
A single module that can be instantiated as either a client (requesting) or server (listening) to enable the interconnection of a systems. Communication can be either a query and response from the client to the server, or broadcast from the server to one or more clients. 

### Plexus
A module that acts as a directory where servers can post their host and port (if using tcp/ip communications) and clients can request them. The name and Channel (Chan) define the address. Often, this is in the form of a system that includes a single Proxy module, known as a **Plexus System**.

### Server 
The server module that spins up an xGraph system in a web browser.

### Viewified (Viewify.js)
An entity whose dispatch table has been run through the **Viewify.js** script. **Viewify.js** provides any entity with the base code to be a View.

### View
A module who's Apex entity's dispatch table has been viewified, or run through the Viewify.js script and contains an actual graphical user interface element that can shown to an end user.

### Discovery
A method for determining other running systems that is only available between systems through a Plexus System where we register servers and can request their Chan and Name.
