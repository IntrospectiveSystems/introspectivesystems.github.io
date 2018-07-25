---
layout: default
title: Build a New Module
permalink: /build-new-module/
tags: ['getting-started']
---

# Setup a New Module

For this example, we will be building a module that is capable of reading and writing text from a file. We
need to know what we want our FileStream module to do before we begin. We want to build a module that is
capable of reading and writing, line by line, to a file. We will start by building a simple module (you can
always expand the functionality to meet your needs later).

To achieve this, we need to be able to tell the module the name or path of the file that we want to read or
write too. Also, to keep things simple, we are going to tell the module whether we want to read or write to
the file. So the module instance will be responsible for reading or writing a file, but not both.

## Generate the new module

First, we will use the xGraph Command Line Interface (CLI) to generate a new module.

In your command line tool, navigate to your root working directory. I will be working in a new project
directory called `Tutorials`.

You can use the `xgraph generate module` command to create a new module. Finnish the command with the name of
the module being generated.

Here I will enter `xgraph generate module Modules\FileStream`. When the module is generated successfully you 
will see a success message with the module's complete path.

![success-message](/assets/images/setup-filestream-module/01.png)

Now I can find my FileStream module's starting files at 
`C:\Users\Introspective\WebstormProjects\Tutorials\Modules\FileStream`

Generate module creates a new module in the path provided. If you give generate module a relative path, or 
simply a module name, it will generate a new module relative to the current working directory (cwd). This 
is either the directory that you are currently in, or the directory set using --cwd.


## Design the Write Mode

We want to build the tests for our system before we get into the programming logic so that we have a good 
idea of what we are building. First, let's explore the tests and logic necessary to get the Write mode of 
the FileStream module 

### Add tests

Open the `test.json` file found in the FileStream module. An empty test file is created when the module is 
generated.

![module-files](/assets/images/setup-filestream-module/02.png)

For all the details on how Validate works, see the `README.md` included in the Validate module. For now we'll 
walk through setting up tests for our FileStream module.

The Validate test objects have two main sections: State and Cases. Open your test.json file and you should 
find an object with these keys.

```
{
  "State": {},
  "Cases": []
}
```

The State section holds a parameter object, listing parameters as key-value pairs that will be passed to the 
FileStream module when it is generated. This allows us to configure the instance of the Module being tested.

For our FileStream module, we will need to pass the module the name of the file we will be accessing, and 
whether we want to read or write to the file. So I need to add two parameters to our `"State"` object: File 
and Mode. 

Now I will add a key `"File"` with the value `"testfile.txt"` and the key `"Mode"` with the value `"write"`.

```
{
  "State": {
    "File": "testfile.txt",
    "Mode": "write"
  },
  "Cases": []
}
```

The Cases section will hold our test cases. These test cases include the command object that will be sent to 
FileStream, as well as the expected reaction to the command. Not only can we check to make sure a command is 
received by a module, we can check to see if the command returns, verify any data it returns, and check to 
see if the command triggered the module to send a message.

The `"Command"` key holds the command object that will be sent to the module when that case is tested. The 
`"Response"` key holds the command object that is expected to be returned to the callback and verifies the 
return values. The key `"SentMessages"` holds an array of messages as command objects, and checks to make 
sure the command triggered FileStream to send these commands.

We know we will need at least two commands in our FileStream module: WriteFile and ReadFile. However, we know 
that we will need to be in the same mode as the command we are trying to execute. That is, to WriteFile, we 
must be in write mode, and to ReadFile, we must be in read mode. 

For now, let's just focus on the WriteFile command.

The WriteFile command needs to include an argument to pass the content we want to write to the file to the 
FileStream module. Let's use the variable `"String"` for our argument. WriteFile does not need to send any 
messages, or return anything. So we can add the following case to our test.json:

```
{
  "State": {
    "file": "testfile.txt"
    "mode": "write"
  },
  "Cases": [
    {
      "Command": {
        "Cmd": "WriteFile",
        "String": "We are going to write this string to a file."
      }
    }
  ]
}
```

Now, if we run our test again using 
`xgraph reset --cwd ./TestSystems/FileSystem/`
we see the test gets sent, but you will get an error because the command doesn't exist. 

Next we will add the WriteFile command to the FileStream module.

### Add the command

Find and open the main entity in the FileStream module, `FileStream.js`.

![module-files](/assets/images/setup-filestream-module/03.png)

Open `FileStream.js` and you will see an empty entity template. The default template uses a JavaScript class 
to define the entity. You can also use a JavaScript function to define an entity. To learn all about xGraph 
entities, see our [Entity Guide](/entities/).

```
class FileStream {
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
};
```

The FileStream class has two methods: Setup and Start. These are the commands the entity accepts, and they 
are both called when the entity is instantiated. All xGraph commands need to accept a command object, com, 
and a callback function, fun. When the command has finished it's work the callback function is called with 
two parameters, the first being a string containing errors, if any, and the second being the command object. 

We need to add a WriteFile command to our entity. Like all xGraph commands, it will need to accept the 
command object, com, and the callback function, fun. When the command has completed, it should call the 
callback function. We can start with the basic layout for the WriteFile command.

```
WriteFile(com, fun){
			
	fun(null, com);
}
```

Before we begin programming the functionality for our module, we will want to keep track of our entities 
properties. An entities parameter object is accessed in a command using `this.Par`, and an entities vault 
object is accessed in a command using `this.Vlt`. Additionally the `this` context object holds access to all 
xGraph functionality, such as `send` and `genModule`. Finally, we want to keep track of any errors, so that 
we can send them back with the callback function.

To keep track of these things, I will add four variables to our commands: Par, Vlt, that and error. `Par`, 
`Vlt`, and `that` will reference the command context objects discussed above. `error` will be set to null 
unless we encounter an error, and then the error message will be saved, and sent back in the callback 
function. 

Now we have updated our commands to include these properties.

```
class FileStream {
    Setup(com, fun) {
        let that = this;
        let Par = this.Par;
        let Vlt = this.Vlt;
        let error = null;

        fun(error, com);
    }

    Start(com, fun){
        let that = this;
        let Par = this.Par;
        let Vlt = this.Vlt;
        let error = null;

        fun(error, com);
    }

    WriteFile(com, fun){
        let that = this;
        let Par = this.Par;
        let Vlt = this.Vlt;
        let error = null;

        fun(error, com);
    }
}
```

Now we can add the programming logic to our code. We will start by considering what will happen when we 
instantiate the FileStream module. We will be using the Node.js File System to handle our FileStream 
capabilities. File System (fs) enables access to the file system in Node.js programs. See the Node.js 
documentation for details about the [Node.js File System](https://nodejs.org/api/fs.html).

In the Setup phase of instantiation, we want to load the Node.js File System and save it to the entities 
vault object, `this.Vlt`. 

```
Setup(com, fun) {
	let that = this;
	let Par = this.Par;
	let Vlt = this.Vlt;
	let error = null;

	let fs = this.require('fs');

	if(fs == null || fs == undefined){
		errors = "Unable to load fs.";
	}


	Vlt.Fs = fs;

    fun(error, com);
    }
```

Here you can see I check to see if the Node.js fs has loaded properly. If it has not, the error will be 
passed back with the callback function, alerting the user to the error.

Next, in the Start phase of instantiation, we can access the file we want to write to. The path of the file 
is provided in the entities parameter object, accessed in `this.Par`. So we will use fs to check to see if 
the file exists, create the file if it doesn't exist, and open a pointer to the file in write mode. Then we 
will save the file descriptor to the entities Vault so that we can use it later. Here is my Start command.

```
Start(com, fun){
	let that = this;
	let Par = this.Par;
	let Vlt = this.Vlt;
	let error = null;


	let fs = Vlt.Fs;


	Vlt.File = "NewFile.csv";
	if(Par.File){
		Vlt.File = Par.File;
	}


	let fd = fs.openSync(Vlt.File, 'a+');
	Vlt.Fd = fd;


	fun(error, com);
}
``` 

Finally, we need to add the logic to the WriteFile command itself. This is easy to do using the 
[Node.js File System](https://nodejs.org/api/fs.html).

```
WriteFile(com, fun){
	let that = this;
	let Par = this.Par;
	let Vlt = this.Vlt;
	let error = null;


	let fs = Vlt.Fs;
	fs.appendFileSync(Vlt.File, com.String);

	fun(error, com);
}
```

Next we will add tests for the read mode.

## Deisgn the read mode

### Add tests

To test the ReadFile command, we need to start the module in "Read" mode. We will need to create another 
test file for this. First, I will rename the `test.json` file to `testWrite.json`. Now we can add another 
test file, `testRead.json`. 

The ReadFile command needs to return an argument so we can include the string that it read from the file. 
Again, I'll use the variable `"String"` for our argument, but this time it will be included in the command 
that we expect to receive in the callback. So we will add a `"Response"` section to our test case where we 
will check to make sure the string is included.

```json
{
  "State": {
    "file": "readfile.txt"
    "mode": "read"
  },
  "Cases": [
    {
      "Command": {
        "Cmd": "ReadFile"
      },
      "Response": {
        "Cmd": "ReadFile",
        "String": "This is the contents of a file."
      }
    } 
  ]
}
```

### Add ReadFile command

The last thing we need to do is add the ReadFile command. Start by adding a ReadFile command, like the one 
below, to the entity's class.

```
WriteFile(com, fun) {
	let that = this;
	let Par = this.Par;
	let Vlt = this.Vlt;
	let error = null;



	fun(error, com);
}
```

All that is left to do is add programming logic to read the file and print it to the console. You can read 
the file using the [Node.js File System](https://nodejs.org/api/fs.html). Try adding the logic and getting 
the tests to pass on your own.