---
layout: default
title: Module Broker
permalink: /module-broker/
tags: ['root', 'modules']
---
# Module Broker

The xGraph module broker provides users with access to all of xGraph's core modules. You can use xGraph core 
modules without downloading the modules by specifying the xGraph module broker as the source for a core 
module.

To load a module from the xGraph module broker, provide the address of the xGraph module broker as the source 
of the module. 

A module broker address is represented by a string with the module broker prefix, `mb://`, followed by a URI 
and a port. If you do not include a port, it will default to 27000. 

The xGraph module broker is hosted at modulebroker.xgraphdev.com and communicates over the 27000 port. 
Following are two examples of a module source pointing to the xGraph module broker.

```
Source: {
  "Core": "md://modulebroker.xgraphdev.com"
}
```
and
```
Source: {
  "Core": "md://modulebroker.xgraphdev.com:27000"
}
```