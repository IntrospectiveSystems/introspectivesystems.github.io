---
layout: default
title: Setup a Test System
permalink: /setup-test-system/
tags: ['getting-started']
---

# Setup a Test System

You can setup a test system for an xGraph module using the [Validate](/Validate/) module. The Validate module 
is great for building and running unit tests of xGraph modules. Here we will walk though the process of 
generating a new system and adding new modules too it.

## Generate a new system

We will use the xGraph Command Line Interface (CLI) to generate a new system. In your command line tool, 
make sure you are in your root working directory. I will still be in the `Tutorials` project directory. 

You can use the `xgraph generate system` command to create a new system. Finnish the command with the name of 
the system being generated. 

Here I will enter `xgraph generate system TestSystems/FileStream`. When the system is generated successfully 
you will see a success message with the system's complete path. 

![success-message]({{ "/assets/images/setup-test-systems/01.png" | absolute_url }})

Generate system creates a new system in the path provided. If you give generate system a relative path or a 
simple name, it will create the system relative to the current working directory (cwd). This is either the 
directory that you are currently in, or the directory set using --cwd.

Now I can find my FileStream test system file, it's `config.json`, in 
`C:\Users\Introspective\WebstormProjects\Tutorials\TestSystems\FileStream`

![FileStream-test-system]({{ "/assets/images/setup-test-systems/02.png" | absolute_url }})

Find the config.json file for the system you just generated. This is where we will add all the modules that 
are included in our system. We will start building our system by adding the modules we will be using to the 
system's structure object. 

Open the `config.json` file in your favorite text editor or JavaScript IDE. 

## Add modules to the system

We are building a system to test our FileStream module as we build it, so we will need to use the Validate 
module to run our tests. Now we need to add the Validate module and the FileStream module to our test 
system's structure file.

In the `config.json` file, you will find an object with three keys: `"Sources"`, `"Modules"` and a subsection 
of modules, `"Deferred"`.  

```
{
	"Sources": {},
	"Modules": {
		"Deferred": []
	}
}
```

### Adding Sources

The `"Sources"` object holds locations where modules can be retrieved. This allows you to define paths for 
module directories and module broker source objects at a system level. 

Sources are recorded using a key, and valued with a path. Then the source key is used to specify module 
sources in the `"Modules"` section of the system structure object. 

Our FileStream system will use modules from two sources: the xGraph core, and our local `.\Modules\` 
directory. We will add two entries to our `"Sources"` object, core and local, where a reference to the xGraph 
core module broker will be stored in `"core"`, and '"local"` will point to the `.\Modules\` directory in our 
`"Tutorial"` directory.

A reference to a module broker is represented as an object with one or two keys: `"Host"` and `"Port"`. To 
connect to the xGraph core module broker, we only need to specify the value of `"Host"`, because the `"Port"` 
is set to the default.

```
{
	"Sources": {
		"core": {
			"Host": "modulebroker.xgraphdev.com"
},
	"local": "./Modules/"
},
    "Modules": {
        "Deferred": []
    }
}
```

### Adding Modules

First we will add the Validate module to the system by adding it's module definition to the `"Modules"` 
object. The Validate module is from the xGraph core, in the xGraph namespace, so we will start the object 
with 

```
{
    "Module": "xGraph.Validate", 
    "Source": "core"
}
```

Additionally, we will need to provide the Validate module's initial parameters. These are listed as keys in 
the `"Par"` object in the module's definition. Validate requires two parameters: TestModule, and TestJson. 
TestModule holds the namespace of the module we will be testing. TestJson specifies the .json file that holds 
the tests that will be run. 

We will be testing our FileStream module, and our test file will be a `test.json` file that we will create in 
the FileStream module after this. Here is our finished module definition object: 

```
{
    "Module": "xGraph.Validate", 
    "Source": "core",
    "Par" : {
        "TestModule": "IS.FileStream",
        "TestJson": "@file ../../Modules/FileStream/test.json"
    }
}
```

We will add this Validate module definition to the system structure object's `"Module"` object, using the key 
`"Validate"`, as below.

```
{
	"Sources": {
		"core": "../xGraph/Modules/",
		"local": "./Modules/"
	},
	"Modules": {
		"Validate": {
			"Module": "xGraph.Validate",
			"Source": "core",
			"Par" : {
				"TestModule": "IS.FileStream",
				"TestJson": "@file: ../../Modules/IS/FileStream/test.json"
			}
		},
		"Deferred": []
	}
}
```

Next, we need to add the FileStream module to the `"Deferred"` section. We need to list the FileStream module 
here because it will be generated by the Validate module. Adding FileStream to the deferred module list will 
pre-load the module into memory, reducing the time it takes for the system to generate the module. 

Typically, any module generated using the genModule command,  `this.genModule(moduleDefinition)`, needs to be 
included in the `"Deferred"` list. Systems that have to generate unknown modules must have permission to do 
so at runtime, using the `--allow-add-module` flag. 

To add FileStream to the deferred module list, we add an object with the module's namespace and source. Here, 
our module's namespace is `"IS.FileStream"`, and the source is local.

```
{
	"Module": "FileStream",
	"Source": "local"
}
```

Finally, we add this object to our system's `"Deferred"` array. Here is our completed `config.json`:
```
{
	"Sources": {
		"core": {
			"Host": "modulebroker.xgraphdev.com"
},
		"local": "./Modules/"
	},
	"Modules": {
		"Validate": {
			"Module": "xGraph.Validate",
			"Source": "core",
			"Par" : {
				"TestModule": "FileStream",
				"TestJson": "@file: ../../Modules/FileStream/test.json"
			}
		},
		"Deferred": [
			{
				"Module": "FileStream",
				"Source": "local"
			}
		]
	}
}
```

Now we can run our system using the xgraph command line interface.

Enter this command into my command line tool,
`xgraph reset --cwd ./TestSystems/FileSystem/`

The system will run, and it should print: 0 Tests, All test concluded, and All tests Passed!

You will write unit tests to include in the FileStream module. This is outlined in the Setup a New 
Module tutorial.
