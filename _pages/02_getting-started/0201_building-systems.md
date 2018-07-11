---
layout: default
title: Building Systems
permalink: /building-systems/
tags: ['getting-started']
---
Building an xGraph system from existing modules is easy. You can generate a new system using the xgraph 
Executable Command Line Interface (CLI). 

To generate a new system with the name `SystemName` use the following command: 
```
xgraph generate system SystemName
```

This command will generate a new system with the name `SystemName`. If you provide the generate system 
command a path instead of a simple string, the module will be generated at the path provided. 

The directory `SystemName` will have a template config.json file in it. Now you can add existing modules to 
your xGraph system by adding them to the `config.json` file.
