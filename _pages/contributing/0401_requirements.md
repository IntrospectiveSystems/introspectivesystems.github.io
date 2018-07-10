---
layout: default
title: Module Documentation Requirements
permalink: /module-documentation-requirements/
tags: ['contributing']
---
This is a guide for creating xGraph module documentation. When building an xGraph module for the module repository, it is important to include the following documentation. This will make it easier for others to find and use your modules.

### Two Types of Documentation
We require our community to include both user and developer documentation for their modules. User documentation must be included as a README.md file, and developer documentation must be included as a README.html file. Next, we will discuss the details for the user and developer documentation.

### User Documentation (README.md)
A README.md file is required for all xGraph Modules. This file provides user documentation. The README.md file should be written to be interpreted by the general public. Below is a list of sections that are required for the README.md, as well as some suggestions for good documentation.

**The following sections must be included in the README.md:**

1. **Module Name.** The name of the module as it appears in the module broker.

2. **Module Version.** The version of the module as it appears in the module broker.

3. **Module Author.** The name of the individual or organization that developed the module.

4. **Module Description.**  A short description (2-3 sentences) in plain English that describes what the module does.This will be the description that displays in the module broker.

5. **Module Interface.** The Module Interface includes details about the Module which are necessary to use the Module in a System. These details are necessary for users to use the xGraph platform, and the module interface section for each module should include the following subsections. 

    1. First, the **module’s parameters** that are set in the ‘Par’ of the Module Definition. We only want to include 
      parameters that are found in the module definition of the module’s Apex. 
    2. Next, the **output commands**, which are the commands the module can send to other modules. Commands that are sent 
       to other entities within the module do not need to be listed here. For each command, this section should include 
       the following subsections: 
    
       1.  the command name 
       2.  a brief description of the command 
       3.  the reference of the Pid the command will be sent to
       4.  a description of the command object to be sent
       5.  a description of the expected returned object
    
    3. Last, the **input commands**, which are the commands the module can receive from other modules. These are all the 
       commands found in, and sent to, the module’s Apex. For each command, the this section should include the following 
       subsections: 



An example of the **module’s definition** should be included in this documentation. This is the JSON format necessary to add a module to a systems definition. Also, add **examples, pictures and walkthroughs** to help people learn how to use your Module. Anything that you think will be helpful to someone trying to use your Module should be added to the README.md file.

Next, we will consider developer documentation, intended to support xGraph module developers and found in the README.html file.

### Developer Documentation (README.html)

A README.html file is required for all xGraph Modules. This file provides developers with documentation so they can build and maintain xGraph Modules. The README.html file should be written to be interpreted by developers experienced in the programming language of the Module. 

For each module, the README.html file will include documentation for each entity and the commands within each entity.

The README.html file should be generated from the JSDoc code documentation. Below is a list of requirements for the code documentation. 

### Code Documentation (JSDoc)

Each entity within a module must be documented using JSDoc format. This is a standard documentation format that enables generation of documentation into html files that are easier to read. Code documentation consists of two types of JSDoc formated comments, which is then generated into an html file.

1. **First, the entity definition should be documented.** To capture these comments with the document generator, these comments must be placed right before the initial function declaration, inside of the first parenthesis. Here, provide a brief description of the entity, and the parameters passed into the entities context. These Parameters can be found in the module’s schema, or in the module definition for a module’s apex entity. An example of this documentation follows.

```
(
/**
 * The HelloWorld entity, in the HelloWorld module, communicates with the    
 * 	HelloWorld2 entity. 
 * @param {object} this.Par     The Parameter object included in the module 
 * 						definition.
 * @param {Pid} this.Par.Friend The Pid of the Friend entity, as defined in 
 * 						the module's schema.
 */
function HelloWorld() {
	...
```

2. Next, each commands within the entity should be documented. To capture these comments with the document generator, these comments must be placed immediately before each Command function. Here, provide a brief description of the command, the parameters passed with the command object, and the returns passed with the return function. Below is a simple example.

```
/**
 * Get module by module name and return full Module.zip
 * @param {Object} com
 * @param {String} com.Name  	Module name as written in module.json
 * @param {String=} com.Version  Module version in semver format,
 * @param {function} fun
 * @return {com.Module} Zipped module in base64
 */
function GetModule(com, fun) {
	...
```

### Documentation Generator Basics
Basic module documentation can be generated with DoxDox. 

DoxDox install instructions: https://doxdox.org/
DoxDox uses JSdoc syntax, for specific syntax information see: http://usejsdoc.org/

DoxDox can be used to create both Markdown, and Html pages. 

To create the full html documentation for the entire module run the following
```
doxdox '*.js' --layout bootstrap -o README.html
```

### Module Definition

Basic Example
```javascript
/**
* Pull a module from another ModuleBroker into this one.
* @param {Object} 	com 		Command object
* @param {String} 	com.Module 	Name of the module to retrieve
* @param {String} 	com.From 	Pid of the other ModuleBroker
* @param {function} fun callback function
* @return {com.Module} Zipped module in base64
*/

```
Add additional @return lines to define additional parameters returned in callback command object.

To define an optional parameter add = to the DataType:
```javascript
/**	 
* 
* @param {String=} 	Example     Optional string parameter
*/
 ```