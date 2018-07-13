---
layout: default
title: Platform
permalink: /platform/
tags: ['root', 'platform']
---
# Platform

Once you've set up your new module, you'll want to add some programming logic. The programming logic for a 
module can be distributed throughout entities and commands. This enables the fractal graph architecture of 
the xGraph platform. 

## The Basics
First, lets cover some basics.

### Fractal Architecture
xGraph modules are structured in a way that enables a base level for our architecture of self-similar layers. 
Each module is comprised of entities, which each consist of a single JavaScript function (or class). The 
entity is the smallest part of an xGraph system. 

In a module, the entities represent the nodes of the graph. In a system, the modules represent the nodes of 
the graph. And in a system of systems, a system can be represented by a single node of the graph. This is the 
fractal nature of xGraph, and we could continue on this way to a system of systems of systems, and so on and 
so forth.

### Apex Entity
Each module has a special entity called the Apex. This entity is identified as the apex in the module's 
structure file, `schema.json`. The Apex of a module handles all incoming commands, and receives the parameters 
defined in the module definition. 

### Module Parameters (this.Par)
Module parameters included in the module definition will be available in the parameter object of the modules 
Apex entity, `this.Par`.

### Entity Vault (this.Vlt)
Global entity variables must be saved in the vault so that they can be shared between commands. Variables can 
be saved as keys in the vault object and accessed through `this.Vlt`.

### Commands
In an xGraph module, each entity must be contained in a single JavaScript file. Each entity has a set of 
commands that it is capable of responding to. xGraph facilitates the communication between nodes using 
command messages. 

These commands are how xGraph nodes relate to each other. As modules are the smallest fractal level, the 
entity is the smallest representation of a node, and the commands between entities act as relationships 
between these nodes.

Each of these commands exists as a function (or method) of the entity function (or class) and expects two 
parameters: com, the command function, and fun, the callback function. The command object is used by xGraph 
to identify relations between nodes, as well as for information passing purposes. The callback function is 
used to return information and program control to the calling function. 

### Additional Notes
Callbacks are often used in asynchronous programming. The xGraph platform is built on Node.js, an 
asynchronous language that uses callbacks. Because xGraph is a platform built on Node.js, most JavaScript 
features and libraries will work with the xGraph platform. 
