---
layout: default
title: Basics
permalink: /basics/
tags: ['root', 'basics']
---
# Basics
xGraph makes development of complex systems easy with its graph architecture of self-similar parts. Systems 
are built from encapsulated parts, called modules. These modules work together to create a whole system. 
Because of its self-similar nature, systems can be built to work as a system of systems where multiple, 
independent systems work together. 

The **xGraph core architecture** allows you to develop complex systems using specialized parts. The xGraph 
core compiles a system from the config.json file, the system configuration file. Then, the xGraph core runs 
the system and handles communication between these parts. 

**Genesis** is responsible for compiling xGraph systems. When Genesis is run with a system definition, the 
system definition is compiled into a Cache. This Cache contains everything the system will need to be run 
using Nexus.

**Nexus** is responsible for running a compiled xGraph system. This means that, given a system’s Cache, Nexus 
initiates the required parts and handles communication between these parts. 

Most of the time developers don’t have to worry about these processes. When developing xGraph systems the 
**[xGraph Executable Command Line Interface (CLI)](2.1-Getting-started-with-xGraph)** will handle compiling 
and running systems. 

## Parts of an xGraph System
Although xGraph parts are self-similar in nature, we have defined specific parts to help developers build 
xGraph systems. Next we will look at the different parts that make up an xGraph system, and how those parts 
work together, is explained.

### System
A **system** is made up of at least one module and is run using the xGraph core. A system is defined by a 
**system structure object** and a **cache**. The system is initially compiled using the system structure 
object found in a config.json file, and it is later run using the compiled cache. To learn more about xGraph 
systems, see our [System Guide](1.2-Systems-Guide).

### Module
A **module** is a fully encapsulated entity or group of entities. Modules are contained in a single directory, 
and are composed of several parts. A module includes:
1. at least one entity 
2. a schema.json file that describes the modules structure
3. a module.json file that describes the modules API
4. a README.md file for user documentation and a README.html file for developer documentation.

A module is defined by the **module definition**, a JSON object that is used to generate a module. To learn 
more about the xGraph module, see our [Module Guide](1.3-Module-Guide).

### Entity
An **entity** is the lowest level of the xGraph self-similar architecture. Entities should be built in 
separate JavaScript files. There should only be one entity in each JavaScript file. 

An xGraph entity is compiled and managed by the xGraph core. An entity is defined by a JSON object. This 
object includes the specific JavaScript file that has the entity’s logic, as well as any parameters the 
entity expects on instantiation, and the commands that will be sent to the entity on instantiation. This 
**entity definition** is used in the schema.json of the module. To learn more about the xGraph entity, see 
our [Entity Guide](1.4-Entity-Guide).

### Command
Each part of an xGraph system will be uniquely identified by a **Pid**. A Pid is a 32 character globally 
unique ID. This can be used for a number of things, but it's primary purpose is to provide an ID for each 
part of an xGraph system so that xGraph can manage the system's structure and communications.

The different parts of an xGraph system can use a pid to send messages, called **commands**, to other parts 
of an xGraph system. The program logic of each piece of an xGraph system is contained in these commands. The 
xGraph platform facilitates the communication of xGraph parts using these commands. 
A command is often returned to the part that sent it with additional information using a **callback** 
function. The use of callback functions are typical with Node.js. A callback is simply a function that is 
passed as a parameter to another function, in our case the commands. xGraph uses callback functions to return 
information to the part that sent a command after the command has been executed, making each xGraph system an 
executable graph.
