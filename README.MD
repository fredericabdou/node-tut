# Node JS Essential Training 
## Prerequisites
### Understanding callback functions: 
Example:
```javascript
function func1(callback) { 
    console.log('hello'); 
    if (typeof callback === "function") { 
        callback();
    }
}

func1(function(){ 
    console.log('world');
}); 
```

Note: keep in mind that in js functions are actually first class objects. You can functions to other functions => callback functions 

### Understanding Promises 

Example1: 
```javascript
//defining a promise: 
let promiseToCleanRoom = new Promise(function(resolve, reject) { 
    //cleaning the room
    let isClean = true; 

    if(isClean) { 
        resolve('Clean'); //Clean is the status of resolve 
    } else { 
        reject('Dirty'); 
    }
});

promiseToCleanRoom.then(function(fromResolve){  //"then" is a method that is called when the promise is resolved
    //then can have inside it a callback function which is eventually caled when the promise is resolved
    //Note that i can receive the status of resolve which is "Clean"
    console.log('the room is ' + fromResolve); //fromResolve = Clean
}).catch(function(fromReject){ 
    console.log('the room is ' + fromReject); 
}); //if we want to reject the promise, we can chain another method called "catch"  

```

Example2: 
```javascript
let cleanRoom = function() {
  return new Promise(function(resolve, reject) {
    resolve('Cleaned The Room');
  });
};

let removeGarbage = function(message) {
  return new Promise(function(resolve, reject) {
    resolve(message + ' remove Garbage');
  });
};

let winIcecream = function(message) {
  return new Promise(function(resolve, reject) {
    resolve( message + ' won Icecream');
  });
};

cleanRoom().then(function(result){
	return removeGarbage(result);
}).then(function(result){
	return winIcecream(result);
}).then(function(result){
	console.log('finished ' + result);
})
// The shorter way: 
//   cleanRoom()
//   .then(removeGarbage)
//   .then(winIcecream)
//   .then(function(message) {
//     console.log('This is the message\n' + message);
//   });
```

### Understanding JS call, apply and bind
Call: 
```javascript
var obj = { num:2 }; 
var addToThis = function(a, b, c) { 
    return this.num + a + b + c; 
};

console.log(addToThis.call(obj, 1, 2, 3)); //function_name.call(obj,function_arguments)
// so call attaches a method to an object as if this method is now part of the object

var arr = [1,2,3];
console.log(addToThis.apply(obj, arr)); //here i should use apply instead of call, because all the arguments of the function are now the arr. So in apply i should use an array for the function arguments. that's the only difference with call. 

//bind returns me a function that i can execute later on! so bind will bind me a method to an object without executing it yet! I will pass the arguments of that function on execution time 
var bound = addToThis.bind(obj); 
console.log(bound(1,2,3)); 
//alternatively you can use the spread operator: (here the spread operator ... is spreading the array to its single values because the array is on the left part of the "=" so it's in the execution part. If the spread operator was in the assignment part it would behave as a gathering operator) 
console.log(bound(...arr)); 
```

### Difference between var and let 
var is function scopped. 
Let is not! 
This is also function scopped! Because a function is an object by definition in JS. So this is bound to the function scope. BUT!!!!!! ARROW FUNCTIONS don't have a this!! because they are not treated as objects!!!! This is why we use arrow functions to solve the issue of this scoping!!!!!! 
NB: if this can't find an object to refer to, it will automatically refer to the WINDOW object in the browser and the OBJECT global object in node js   




## Node Core 
### The Global Object
If you're use to using JavaScript in the browser, then you're probably pretty use to the window being the global object. In node.js, the global object is global. 

Please check the [Node JS API](https://nodejs.org/api/globals.html) documentation to check the list of objects that are available to us globally on the global name space: 

<b><u>Global Objects:</b></u>
    Class: Buffer
    __dirname
    __filename
    clearImmediate(immediateObject)
    clearInterval(intervalObject)
    clearTimeout(timeoutObject)
    console
    exports
    global
    module
    process
    require()
    setImmediate(callback[, ...args])
    setInterval(callback, delay[, ...args])
    setTimeout(callback, delay[, ...args])

To run a node file by the following terminal command: 
```
node file_name.js
```

Example1: 
```javascript
/*
The console object is available to us globally because it is a part of the global name space. So, by adding the global name space before this console log, this will actually work the same as without the "global." namespace 

Because the global name space is assumed, we do not have to always include it.
*/
global.console.log("hello world"); 
```

Example2: 
```javascript
/*
Note: Let's see what happens when I create a variable. So, here I've created a variable for Hello, and that variable will be set to a string. If you're use to using javascript in the window (the global object of the web browser), you know that these variables are added to the global object. That means, we should be able to see our Hello variable by typing global.hello.
*/

var hello = "hello world";
console.log(global.hello); 

/*
this time when I run it, I see an undefined. That is because node js works a little bit different than the browser when it comes to storing variables. 

The Explanation: 
Every node js file that we create is it's own module. Any variable that we create in a node js file, is scoped only to that module. That means that our variables are not added to the global object the way that they are in the browser.
*/

// So we can't use the global namespace in that case:
var hello = "hello world";
console.log(global.hello); 
```

Example 3: 
```javascript

console.log(__dirname); //If I were to log __dirname. We would get the full path to the current directory where this module is located. 

console.log(__filename); //If I were to log__file name, we can get the full path to the current file as well as this file's name.

```

Example4: 
```javascript

var path = require('path'); 

/*
The require function, is what we're going to use to import other node js modules.
The path module, is a module that is available to you with your installation of node js. It gives us some tools for working with paths. 
*/
```

### Argument variables with process.argv 
One important object that is available to us globally is the process object. It can be accessed from anywhere, and it contains functionality that allows us to interact with information about the current process instance. We can use the process object to get environment information, read environment variables, communicate with the terminal, or parent processes, through standard input and standard output. We can even exit the current process. This object essentially gives us a way to work with the current process instance. One of the the things that we can do with the process object is to collect all the information from the terminal, or command prompt, when the application starts.

All of this information will be saved in a variable called process.argv which stands for the argument variables used to start the process. 

"process" is available to us in any module globally. So we can use the process object wherever we like.

```javascript
console.log(process.argv); 
/*
we can see that the process.argv variable is an array. It contains a path to node, and a path to app.js. We started the app by running node. User/local/bin is the directory that that executable is found in. We also started this app.js file and we can see that we have the full path to that here.
*/
```
In our terminal we can add a flag represented by --flag_name flag_value to the node command and it will store this flag name and value as 2 separate values in the process.argv array. NB: we can add antyhing !!! Not just a flag anything we want separated by SPACES!!! AND THEY WILL BE STORED IN THE process.argv array!! 

```bash
node file_name.js EXAMPLE1 EXAMPLE2 EXAMPLE3
#EXAMPLE1 EXAMPLE2 and EXAMPLE3 will be added to the process.argv array! 
```

### Standard input and standard output
Another feature of the process object is standard input and standard output. These two objects offer us a way to communicate with a process while it is running. For now, we will use these objects to read and write data to the Terminal. Later on in the course, we're going to use the standard input and standard output objects to communicate with a child process.

```javascript
/*
Let's go ahead and write a message to the console just using standard output. process.stdout is our standard output object and we can use the write method to write some strings to the Terminal.
*/

process.stdout.write("What is your name?\n"); 

/*
Let's go ahead and add a listener to our program to listen for the answer to our question.
I'm going to wire up an event listener for data on the standard input object.
*/

process.stdin.on('data', function(data) { 
    process.stdout.write('\n' + data.toString().trim() + '\n'); 
    process.exit; // process.exit will cause the process to exit from within.
});

/*
So when a data event is raised, this means that when the user types some data into the Terminal and hits enter we will raise this data event here.

Role of the callback function:  So I'm going to go ahead and add a callback function to handle this.
So when the user enters some data, or when any data is sent to our application through the standard input object this callback function will fire and that data that is sent to our application will come in as an argument. 

So when there's any data sent to this application through the Terminal this callback function will fire and we will echo the data back by writing it to the Terminal and displaying it to our user. 
*/

process.on('exit', function() { 
    process.stdout.write("BYE!\n"); 
});

/*
process.on('exit', callback_function()) will listen for an exit event on the process object.

And when the process.exit is invoked this callback function will fire. So when we exit the process we can do a couple of things just before the process exits.
*/
```
So process.stdin and process.stdout are ways that we can communicate with a running process.

### Global Timing Functions 
In the last lesson we started working with Node.js asynchronously by using event listeners. Another way we can work with Node.js asynchronously is through using the timing functions. The timing functions setTimeout, clearTimeout, setInterval, and clearInterval work the same way they do in the browser and are available to you globally.

setTimeout signature: 
```javascript
setTimeout(callback_function, waiting_time)
```

```javascript
//setInterval:
var currentTime = 0;
var waitInterval = 500; 

var interval = setInterval(function() { 
    currentTime += waitInterval; 
    console.log (`
    Waiting for: ${currentTime/1000} seconds
    `);
}, waitInterval); 

// setTimeout will create a delay of a certain time and then invoke a callback function. 
var waitTime = 3000; 
console.log("wait for it!");

setTimeout(function() { 
        clearInterval(interval); 
        console.log("done"); 
}, waitTime); //So what we're going to do is invoke this function after our delay

/*
. What we're going to do is cause a timeout that will cause this application to wait for three seconds and then it will invoke this callback function where we will simply console.log a done. 
*/
```


## Node Modules
### Core Modules
The require function is what we use to load modules. Many of the modules that we use are hosted the NPM registry and need to be installed first. 

We're going to focus on those modules that you do not have to install with NPM. These modules were installed locally with your installation of node JS. We refer to these modules as core modules. And path is one of the core modules available to you with node JS. 

Example1: 
```javascript

var path = require('path'); 
console.log(path.basename(__filename)); ////We use the path module for instance to pluck the base file name from a full path

var dirUploads = path.join(__dirname, 'www', 'files', 'test'); // We can also use the path module to create path strings. The path dot join function can be used to join strings together in a path. 
console.log(dirUploads); 
```
Example2: 
```javascript
/*
The utilities module is called util. The utilities module has several helper functions that we can use. One of which is a log. So instead of doing console logs, I can do util logs. And, the difference is we're still going to log this information to the console. The utilitie module log function also adds a date and time stamp. 
*/
var util = require('util'); 
util.log('hello world'); 
```

Example3: 

```javascript
/*
Since, no JS is built on top of Google Chrome's v8 processor, we can use the v8 module to get information about memory.
*/
var v8 = require('v8'); 
console.log(v8.getHeapStatistics()); 
```

### Collecting Information with Readline global object
Readline is a module that allows us to ask questions of our Terminal user. It is a wraparound the standard input and standard output objects that allow us to easily control prompting a user with questions and saving those answers. 

```javascript
var readline = require('readline'); 
//once I have readline, I can create an instance of the readline object which will create prompt for me by sending it the standard input and standard output objects.

var rl = readline.createInterface(process.stdin, process.stdout); 
/*
So var rl will be my readline instance and I will use readline to create an interface. With this interface, we're going to send it the process.stdin and process.stdout.

Readline is going to control these objects for us asking questions and collecting information so that we don't have to control the process.stdin and stdout directly.
*/

rl.question("what is the name of a real person?", function(answer){
    console.log(answer); 
});

/*
 In order to ask a question of Readline, all you need to do is invoke rl.question. And the first argument will be the question that will show up in the Terminal. We will ask, "What is the name of a real person?" The second argument is the function that will invoke once we have an answer from the Terminal. And in this function, the answer will be sent as an argument.

We can go ahead and just log that answer to the console. 
*/

```

Check this complete example: 
```javascript

var readline = require('readline');
var rl = readline.createInterface(process.stdin, process.stdout);

var realPerson = {
	name: '',
	sayings: []
};


rl.question("What is the name of a real person? ", function(answer) {

	realPerson.name = answer;

	rl.setPrompt(`What would ${realPerson.name} say? `);

	rl.prompt();

	rl.on('line', function(saying) {

		realPerson.sayings.push(saying.trim());

		if (saying.toLowerCase().trim() === 'exit') {
			rl.close();
		} else {
			rl.setPrompt(`What else would ${realPerson.name} say? ('exit' to leave) `);
		    rl.prompt();
		}

	});

});


rl.on('close', function() {

	console.log("%s is a real person that says %j", realPerson.name, realPerson.sayings);
	process.exit();
	
});


```

### Handling Events with EventEmitter 
Another powerful feature that ships with Node.js is the Event Emitter. The Event Emitter is Node.js's implementation of the pub/sub design pattern, and it allows us to create listeners for an emit custom Events. In fact, every time we've used that on function to listen for a new Event, we've already been using an implementation of the EventEmitter.

The EventEmitter provides us a way to create custom objects that raise custom events that can be handled asynchronously. And because the events are handled asynchronously, it is a very important tool in node.js.

Example1: 
```javascript
var events = require('events'); 

var emitter = new events.EventEmitter(); 
//the EventEmitter itself is a constructor. So, I'm going to create a new instance of a variable called emitter. This object that we created has "on" and "emit" functions 

emitter.on('customEvent', function(message, status){ 
    console.log(`${status}: ${message}`);
})
/*
So, every time we use on, we can wire up a custom event. You can name an event whatever you like. In this case, I've just called this customEvent. The second argument that the on function takes is a callback function that will be invoked when the custom event is raised. In this case, our custom event is going to pass a message and a status to this function as arguments. So, when our custom event occurs, this callback function will be invoked asynchronously. 

So, when a custom event is raised, we'll pass a message and a status to this callback function asynchronously, and we're just going to log that message in status.

The next part of the EventEmitter is the ability to trigger or emit custom events. We can trigger or emit a custom event with the emit function.
*/

emitter.emit('customEvent', 'hello world', 200); // So, emitter.emit will fire our custom event.

/*
The first argument is the event name that we want to fire. And then, the next arguments are going to be the arguments that will passed to the callback function. So, the first argument in the callback function, the message, is actually the second argument of this emit function. So, for the message, I will send Hello World. And the third argument is going to be the second argument in the callback. So, I will send a status of 200. In this code, we've created a new instance of the EventEmitter object, and we wired up a listener to listen for custom events.
*/

```
The EventEmitter is rarely used as a standalone object. We can really get mileage out of it by allowing our objects to inherit the EventEmitter. 

Example2: 
```javascript
//I'm going to create a var called util, and I'm going to require our utilities module. The utilities module has an inherits function, and it's a way that we can add a object to the prototype of an existing object. That's how JavaScript handles inheritance. 

/*

The first thing that I want to do is instead of including just the events up here on line one, we can actually pull the EventEmitter out of events directly in this require statement here. So, I will use EventEmitter as a variable. And then, I can just chain on to the end of this require statement EventEmitter. And that will pull that constructor function out of the events module and set this variable to our new constructor function.

*/
var EventEmitter = require('events').EventEmitter;
var util = require('util');

var Person = function(name) {
	this.name = name;
};

util.inherits(Person, EventEmitter);

var ben = new Person("Ben Franklin");

ben.on('speak', function(said) {

	console.log(`${this.name}: ${said}`);

});


ben.emit('speak', "You may delay, but time will not.");
```

### Exporting Custom Modules
In Node.js, every Javascript file is a module. We've been loading external modules with the require function. The require function is part of the common JS module pattern, but it only represents half of the pattern, the half that loads the modules. The other half of the pattern is module.exports, or the mechanism that we can use to make our modules consumable.

In the next example create 2 files: file1.js and file2.js

Code for file1:
```javascript
var Person = require("./file2"); //When requiring modules, we do not include the .js extension, it just assumes that this file is Javascript, so we are looking for the person Javascript module.

var ben = new Person("Ben Franklin");
var george = new Person("George Washington");


george.on('speak', function(said) {

	console.log(`${this.name} -> ${said}`);

});

ben.on('speak', function(said) {

	console.log(`${this.name}: ${said}`);

});


ben.emit('speak', "You may delay, but time will not.");
george.emit('speak', "It is far better to be alone, than to be in bad company.");
```

Code for file2: 
```javascript
//This is our reusable module

//Now that we have this code here, these variables that we create in this file are local to this module. 

//That means that everything in this file is private and cannot be consumable by another module. If we would like to make items consumable by other Javascript files, we can export them on module export.


var EventEmitter = require('events').EventEmitter;
var util = require('util');

var Person = function(name) {
	this.name = name;
};

util.inherits(Person, EventEmitter);


module.exports = Person;
/*
Module.exports is a Javascript object. We can use it like any Javascript object. We can dot notate on it, bracket notate, set it to an object literal or any Javascript type. In this case, I'm setting module.exports to our person constructor function.

Module.exports is the object that is returned by the require statement. When we require this module, we will return anything that is on module.exports. 
*/

```

### Creating child processes with exec
Node.js comes with a Child Process module which allows you to execute external processes in your environment. In other words, your Node.js app can run and communicate with other applications on the computer that it is hosting. 

We're going to take a look at the two main functions used to create Child Processes: spawn and execute. 

Note that with the Node.js execute function we can actually execute external terminal commands (such as ls, cd etc.) from our Node.js modules. 

With the Child Process module we have an execute function. 

```javascript 
/*
The Child Process module contains the execute function, so I'm just going to chain this on the end so I can pluck it out into my execute variable. Now with the exec function I can execute commands. I can exec, for instance, an open, and now when I actually save and run this node module we will go ahead and excecute a ls -la. 
*/

const { exec } = require('child_process'); 
/* 
In the above line we have a destructuring:
It's a shorthand for: 

const {exec: exec} = require('child_process');
    Or

var exec = require('child_process').exec;
*/
exec('ls -la', function(err, output, std_err){ 
    if (err) { 
        throw err; 
    }
    console.log(output);    
    if (std_err) { 
        console.log(std_err);    
    }
        
});

// Now every time we've been executing any of these processes any data that gets returned by the process would be returned to the second argument in the execute function, a call back function.
```

### Creating child processes with spawn 
So if we have these processes that have small bits of data, they're perfect for execute. So spawn is made for longer, ongoing processes with large amounts of data.

create 2 files: 
spawn.js: 
```javascript 
var spawn = require("child_process").spawn;

var cp = spawn("node", ["alwaysTalking"]); 
/* So now I have my spawn function set up. This time I'm going to create a variable for the child process, and that will be returned by my spawn function. The first argument that I'm going to send to the spawn function is the command that I want to run in the terminal. That is node. The second argument is going to be an array of all of the things that I would run after the node command.

So in order to run the alwaysTalking app we will node alwaysTalking in the command line, so I'll just go ahead and put alwaysTalking or the options that would show up in the command line after node into an array, which represents the second argument of the spawn function.
*/

cp.stdout.on("data", function(data) {
	console.log(`STDOUT: ${data.toString()}`);
});

cp.on("close", function() {

	console.log("Child Process has ended");

	process.exit();

});


setTimeout(function() {

	cp.stdin.write("stop");

}, 4000);
```

alwaysTalking.js
```javascript
var sayings = [
    "You may delay, but time will not.",
    "Tell me and I forget. Teach me and I remember. Involve me and I learn.",
    "It takes many good deeds to build a good reputation, and only one bad one to lose it.",
    "Early to bed and early to rise makes a man healthy, wealthy and wise.",
    "By failing to prepare, you are preparing to fail.",
    "An investment in knowledge pays the best interest.",
    "Well done is better than well said."
];

var interval = setInterval(function() {
	var i = Math.floor(Math.random() * sayings.length);
	process.stdout.write(`${sayings[i]} \n`);
}, 1000);

process.stdin.on('data', function(data) {
	console.log(`STDIN Data Recieved -> ${data.toString().trim()}`);
	clearInterval(interval);
	process.exit();
});
```


## The File System 
Node.js also ships with a module that allows us to interact with the file system. The fs module can be used to list files and directories, create files and directories, stream files, write files, read files, modify file permissions or just about anything that you need to be able to do with the file system.

### Listing directory files
Every object function in fs has a synchronous and asynchronous version 

Synchronous version: 
```javascript 
var fs = require("fs"); 
var files = fs.readdirSync('./folder_name'); 
console.log(files); 
```

Asynchronous version:
```javascript 
var fs = require("fs");

fs.readdir('./folder_name', function(err, files) {
	if (err) {
		throw err;
	}
	console.log(files);
});

console.log("Reading Files...");
```

### Reading files
Another feature of the fs module is the ability to read the contents of files. We can read the contents of both text and binary files. If we are reading the contents of a text file, we have to make sure that we send the read file function a text encoding, like UTF-8, otherwise it will automatically read our files as binary, giving us the Node.js buffer class.

Synchronous Version
```javascript
var fs = require("fs"); 
var contents = fs.readFileSync('./file_name', 'UTF-8');
/* 
if we ommit UTF-8 it will read it as a binary 
console.log(contents);
And binary files in Node.js are handled with the Node.js buffer class. 
```

Asynchronous Version
```javascript
var fs = require("fs"); 
var path = require("path"); 

fs.readdir("../My Files", function(err, files_array){ 
    files_array.forEach(function(fileName){ 
        var file = path.join('../My Files', fileName); 
        var stats = fs.statSync(file); //it will tell wether it's a file or directory
        if (stats.isFile() && fileName !== '.DS_Store') { 
            fs.readFile(file, 'UTF-8', function(err, contents){ 
                if (err) { 
                    console.log(err); //logging the erro won't kill the process, but throwing it will! be careful
                }
                console.log(contents);
            });
        }

    })
});

```

### Writing and appending files 
Another feature of the file system module is the ability to create new files, to write text or binary content to those files, or to append text or binary content to an existing file. 

Example:
```javascript
var fs = require("fs"); 
var md = `hello world`;
//creating a file asynchronously 
fs.writeFile("file_to_create.extension", "file_content_to_add", function(err){
    if (err) { 
        console.err(err); 
    }
    console.log("file created");
});

//appending content to an existing file asynchronously 
fs.appendFile("already_create_file.extension", "content_to_append", function(err){ 
        if (err) { 
        console.err(err); 
        }
         console.log("content appendded");
});
```

### Directory creation

Example:
```javascript
var fs = require("fs");

if (fs.existsSync("dir_name")) { //checking for the existence of a directory. exists can also be used to check for the existence of a file. Here we are using the synchronous version of it. 
	console.log("Directory already there");
} else {

	fs.mkdir("dir_name", function(err) {

		if (err) {
			console.log(err);
		} else {
			console.log("Directory Created");
		}

	});

}
```

### Renaming and removing files 
Renaming and moving example: 
```javascript
var fs = require("fs");


fs.renameSync("./lib/project-config.js", "./lib/config.json"); //synchronously

console.log("Config json file renamed");


//Moving a file 
fs.rename("./lib/notes.md", "./notes.md", function(err) { //asynchronously... Note: here we are moving notes.md to the parent directory of lib. 

	if (err) {
		console.log(err);
	} else {
		console.log("Notes.md moved successfully");
	}

});
```

Removing Example:
```javascript
var fs = require("fs");


/*
Here, I will do this synchronously, and we will remove from the library folder the config.json file. Again, because this is a synchronous request, if there were any problems with this request, it would automatically throw an error. If I don't want my code to throw an error, when making a synchronous request, I need to surround it in a try catch block. 

Note: throwing an error will make the process stop!
*/
try {
	fs.unlinkSync("./lib/config.json"); //synchronously
} catch (err) {
	console.log(err);
}

fs.unlink("notes.md", function(err) { //asynchronously 

	if (err) {
		console.log(err);
	} else {
		console.log("Notes.md removed");
    }
    
/*
So, what happens when I try to remove files that aren't there? Ig we run the code, we can see that we have two errors. Now, both of these errors are simply being logged to the console. They are not being thrown. When you throw an error, it will cause your program to crash.
*/    
});
```
### Renaming and removing directories 
Example1: Renaming and moving directories with fs.rename
```javascript
var fs = require("fs"); 
fs.renameSync("./assets/logs", "./logs_new"); //moving and renaming logs directory
```

Example2:
```javascript 
var fs = require("fs");

fs.readdirSync("./logs").forEach(function(fileName) { //READING ALL THE FILES FROM THE DIRECTORY AND LIST THEM (returns and array list). Then we are chaining a forEach method to that returned array list :) 
/*
So this will give me a list of those files. Now the neat thing is I could set a variable to all the files that are in that directory, but this is JavaScript, which means that we can also chain. So because this call returns an array I can simply chain on a .forEach, which is a JavaScript array function, that will take in a callback function that will be invoked once for every item inside of the array. Each file name will be passed to this callback function. So we are looping through all of the files that are found in the logs directory, and what I'm going to go ahead and do is unlink them.
*/
	fs.unlinkSync("./logs/" + fileName);

});

//NOTE: YOU CAN NOT REMOVE A DIRECTORY UNLESS IT IS EMPTY! SO YOU SHOULD FIRST REMOVE ALL THE FILES INSIDE THE DIRECTORY BEFORE REMOVING THE DIRECTORY 
fs.rmdir("./logs", function(err) {  //rmdir will remove a directory 

	if (err) {
		throw err; //throwing an error will cause our program to crash and our JavaScript thread will not continue processing anymore JavaScript,
	}

	console.log("Logs directory removed");

});
```
### Readable file streams
Streams give us a way to asynchronously handle continuous data flows. Understanding how streams work will dramatically improve the way your application handles large data. Streams in Node.js are implementations of the underlying abstract extreme interface and we've already been using them. 

process.stdout and process.stdin use the stream interface.

Process.stdout is what we've been using to write data to the terminal, but stdout is really a writeable stream. We send data chunks to it using the write method. Take a look at the code that we've written on line 14 where we used process.stdin or process standard input.

We are listening for a data event. Process standard input implements a readable stream. Whenever a data event is raised, some data is passed to the call back function. So, we've been using streams because process.stdin and process.stdout implement the stream interface. Streams can be readable, like stdin, writeable like standard output, or duplex, which means they are both readable and writeable. Streams can work with binary data or data encoded in a text format like UTF-8. Let's consider how working with streams may allow us to improve our application.

Take a look at this example first:
```javascript
var fs = require("fs"); 
fs.readFile("./chat/log", "UTF-8", function(err, chatlog){ 
    console.log(`File Read ${chatlog.length}`); 
});
console.log('Reading File'); 
```
And now, this will read the file and it works relatively fast but the problem is readFile waits until the entire file is read before invoking the call back and passing the file contents.

It also buffers the entire file in one variable. If our big data app experiences heavy traffic, read file is going to create latency and could impact our memory. So a better solution might be to implement a readable stream. 

Implementing a readable stream Example: 
```javascript
var fs = require("fs");

var stream = fs.createReadStream("./chat.log", "UTF-8");
//Great, so now, as opposed to waiting for the entire file to be read, we can use this stream to start receiving small chunks of data from this file.

var data = "";


stream.once("data", function() {
	console.log("\n\n\n");
	console.log("Started Reading File");
	console.log("\n\n\n");
});

stream.on("data", function(chunk) {
	process.stdout.write(`  chunk: ${chunk.length} |`);
	data += chunk;
}); 

stream.on("end", function() {
	console.log("\n\n\n");
	console.log(`Finished Reading File ${data.length}`);
	console.log("\n\n\n");
});

```

### Writable File Streams
The writable stream is used to write the data chunks that are going to be read by the readable streams.

Example:
```javascript
var readline = require('readline');
var rl = readline.createInterface(process.stdin, process.stdout);
var fs = require("fs");

var realPerson = {
	name: '',
	sayings: []
};


rl.question("What is the name of a real person? ", function(answer) {

	realPerson.name = answer;

	//
	//	TODO: Use a Writable Stream
	//
	fs.writeFileSync(realPerson.name + ".md", `${realPerson.name}\n==================\n\n`);

	rl.setPrompt(`What would ${realPerson.name} say? `);

	rl.prompt();

	rl.on('line', function(saying) {

		realPerson.sayings.push(saying.trim());

		//
		//TODO: Write to the stream
		//
		fs.appendFileSync(realPerson.name + ".md", `* ${saying.trim()} \n`);


		if (saying.toLowerCase().trim() === 'exit') {
			rl.close();
		} else {
			rl.setPrompt(`What else would ${realPerson.name} say? ('exit' to leave) `);
		    rl.prompt();
		}

	});

});


rl.on('close', function() {

	console.log("%s is a real person that says %j", realPerson.name, realPerson.sayings);
	process.exit();

});

```

## The HTTP Module
### Making a request
In this next chapter, we're going to dive deep into Node.js's HTTP module. The HTTP module will be used for creating web servers, for making requests, for handling responses. There are two modules for this. There's the HTTP module and the HTTPS module. Now, both of these modules are very, very similar, but we'll only use the HTTPS module when we're working with a secure server. So that means if we want to create an HTTPS server, we would use the HTTPS module, and then we would have to supply the security certificate.

With the HTTP module, there's no need to supply a security certificate.

Example: 
```javascript
var https = require("https");
var fs = require("fs");

var options = {
	hostname: "en.wikipedia.org",
	port: 443,
	path: "/wiki/George_Washington",
	method: "GET"
};

var req = https.request(options, function(res) {
    /*
    Once our request has started, this callback function, the second argument of the request function will be invoked. It will also add our response object. Now, I said "once the request has started", because our response object actually implements the stream interface. What we're going to do with this request is we are going to get George Washington's Wikipedia page sent to us from the Wikipedia server as a stream. The first thing I'm going to do is create a variable for the response body and we will set that to an empty stream.
    */

	var responseBody = "";

	console.log("Response from server started.");
	console.log(`Server Status: ${res.statusCode} `);
	console.log("Response Headers: %j", res.headers);

	res.setEncoding("UTF-8");

	res.once("data", function(chunk) { // Once we started receiving data (listener) we will console.log only the first chunk
		console.log(chunk);
	});

	res.on("data", function(chunk) { // For all the data received (for every data event) (listener) we will store every chunk of this data in the responseBody variable 
		console.log(`--chunk-- ${chunk.length}`);
		responseBody += chunk;
	});

	res.on("end", function() { // Once our response is over (listener) we will take the content fetched and create a file to put the received content 
		fs.writeFile("george-washington.html", responseBody, function(err) {
			if (err) {
				throw err;
			}
			console.log("File Downloaded");
		});
	});

});

req.on("error", function(err) { // We are listening for any occurence of an error and console.logging it 
	console.log(`problem with request: ${err.message}`);
});

req.end(); //we are ending the request 
```

### Building a web server
One of the coolest things that we can do with Node.js is build web servers. Node.js is JavaScript, the language of the web. So of course, one of the most important features is the ability to create web servers or you can additionally use the HTTPS module that ships with Node.js. Both of these modules will allow you to create web servers, but if you need to create a secure web server, you will need to add a security certificate to the HTTPS module. You can find the instructions on how to do that [here](https://nodejs.org/api/https.html) in the Node.js API documentation.

We're going to be working with the HTTP module, so there will be no need for us to add a security certificate. 

Example: 
```javascript

var http = require("http");
/*
So I'm going to create a variable for my server instance, and I will use http.createServer to build this web server. Now, every time we make a request of our web server, the callback function that we add to create.Server will be invoked. So any request of our web server will cause this function to be invoked. What we are going to get with this function is the actual request object. This request object will contain information about the requested headers, any data that is going along with the request, as well as information about our user, like their environment and so on and so forth.

The other argument that we'll be adding here is going to be our response object. So, we will have a blank response object also sent to this request function, and it's going to be our job to complete the response. 
*/

var server = http.createServer(function(req, res) {

    /*
    We will do so by writing the response headers. So I'm going to use the response.writeHead method to complete our response headers. The first argument that we add to this method is going to be our response status code. 200 means that we have a successful response. The second argument represents a JavaScript literal of all the headers that I am going to add to this response.

The one header that I really want to add is "Content-Type", and this will tell the browser what type of content we are responding with. For this server, we're just going to respond with "text/html".
*/

    res.writeHead(200, {"Content-Type": "text/html"});
    
    /*
     And once we've written the response headers, the next thing that we want to do is end the response and send some data. Res.end can be used to end our response. 
     */

	res.end(`
		<!DOCTYPE html>
		<html>
			<head>
				<title>HTML Response</title>
			</head>
			<body>
				<h1>Serving HTML Text</h1>
				<p>${req.url}</p>
				<p>${req.method}</p>
			</body>
		</html>
	`);

});

server.listen(3000); //Finally we need to tell this server instance what IP and port it should be listening for incoming requests on.

//Server.listen is a function that we can use to specific the IP address and incoming port for all of our web requests for this server. I'm going to add (3000), telling this server to listen to any requests on this local machine for port 3000. 

console.log("Server listening on port 3000");

```

### Serving Files (building a file server)
Example: 

fileserver.js:
```javascript
var http = require("http");
var fs = require("fs"); 
var path = require("path");

http.createServer(function(req, res) {

	console.log(`${req.method} request for ${req.url}`);

	if (req.url === "/") {
		fs.readFile(__dirname + "./public/index.html", "UTF-8", function(err, html) {
			res.writeHead(200, {"Content-Type": "text/html"});
			res.end(html);
		});
/*
So before writing the next code, I'm going to go over to the browser and open up a new browser window, and hit "http:localhost:3000", and when I do so, we can see that we are serving our html file. Now, if I go back to the terminal, one thing you'll notice is, making a request for this html file also makes a request for a stylesheet, also makes a request for a birds.jpg, and our browser is also making a request for a favorite icon icon file.

So, just requesting one html file has caused all these other requests to occur. The issue is, is that if we try to request our stylesheet, we don't get to see it. That's 404, because any request that is not our home page is giving us a 404 right now. That also includes birds.jpg. So, we need to improve our web server to also additionally serve these files.
*/
    } else if (req.url.match(/.css$/)) { //we can check if the requested url matches that regular expression    
    
    /*
    So, if we do have a file with a .css extension, we need to serve that file, too. Now, of course, I could just fs.readFile the way that I've done above, that's one way to do it, but remember, our problem with reading the files, we have to wait until the entire file is read, and then we're going to respond with the entire file. So, what would actually be a better solution is to create a ReadStream.
    */

		var cssPath = path.join(__dirname, 'public', req.url);
		var fileStream = fs.createReadStream(cssPath, "UTF-8");

        res.writeHead(200, {"Content-Type": "text/css"});
        
        /*
         And now, finally, once we have a ReadStream, we can actually pipe a ReadStream to a writable stream. Our response object is a writable stream, so what I'm going to actually do is use the fileStream and it has a pipe method.

         We can take a ReadStream and pipe it to a writable stream using this method .pipe(). So what this will do is it will actually stream the contents of our file to our response and it will automatically handle when that response is over and chunking the data and everything for us. So, this is great. This will actually send our .css file back. 
        */

		fileStream.pipe(res); //here we are piping our readstream: fileStream to a writable stream wich is res (our response itself is a writable stream)

	} else if (req.url.match(/.jpg$/)) {

		var imgPath = path.join(__dirname, 'public', req.url);
		var imgStream = fs.createReadStream(imgPath); //here we don't need to set to utf8, because we are reading the image as a binary 

		res.writeHead(200, {"Content-Type": "image/jpeg"});

		imgStream.pipe(res);

	} else {
		res.writeHead(404, {"Content-Type": "text/plain"});
		res.end("404 File Not Found");
	}

}).listen(3000);


console.log("File server running on port 3000");
```
Create another directory "public" with the following files: birds.jpg, index.html, style.css. 


### Serving JSON data

Example:
Create api.js file 
Create a directory: data. Inside it create an "inventory.json" file. 

inventory.json
```javascript
[
  {
    "name": "K-Eco 180",
    "sku": "KE180",
    "cost": "$162.00",
    "retail": "$315.00",
    "avail": "In stock"
  },
  {
    "name": "K-Eco 200",
    "sku": "KE200",
    "cost": "$180.00",
    "retail": "$350.00",
    "avail": "In stock"
  },
  {
    "name": "K-Eco 225",
    "sku": "KE225",
    "cost": "$198.00",
    "retail": "$385.00",
    "avail": "On back order"
  },
  {
    "name": "K-Eco 250",
    "sku": "KE250",
    "cost": "$225.00",
    "retail": "$437.50",
    "avail": "In stock"
  }
]
```
api.js: 
```javascript
var http = require("http");

var data = require("./data/inventory");

http.createServer(function(req, res) {

	if (req.url === "/") {
		res.writeHead(200, {"Content-Type": "text/json"});
	    res.end(JSON.stringify(data));
	} else if (req.url === "/instock") {
		listInStock(res);
	} else if (req.url === "/onorder") {
		listOnBackOrder(res);
	} else {
		res.writeHead(404, {"Content-Type": "text/plain"});
		res.end("Whoops... Data not found");
	}

	

}).listen(3000);

console.log("Server listening on port 3000");


function listInStock(res) {

	var inStock = data.filter(function(item) {
		return item.avail === "In stock";
    });

    res.end(JSON.stringify(inStock));
    
    /*
    . So, the data is an array, it has a filter function. We use the filter function to filter out those data objects in our array for specific details. So, this function will take in a callback function, and this callback function will be invoked once for every item in the data, so, once for every one of our inventory items.

    And now, this function is what we would refer to as a Predicate. It should only return a True or a False. If this function returns True, then we are going to add this data item to a new array. If it returns False, then we will skip adding this inventory to our new array. So, what we want to do is return a True or False, as to whether this inventory item should be added to the new array. So, what we're going to do, is, the inventory item itself, is going to be passed as an argument to the callback function inside of the filter function.

    What we want to do now, is, return either True or False. And, we're going to return whether or not the items available key is equal to In stock. So, for every item that has the availability marked In stock, those items will be added to this new array.

    */

}

function listOnBackOrder(res) {

	var onOrder = data.filter(function(item) {
		return item.avail === "On back order";
	});

	res.end(JSON.stringify(onOrder));

}
```

### Collecting POST data
So far we have created servers using the http module that only handled GET requests. We can use the http module to create servers that also handle POST requests, PUT requests, DELETE requests, and many others. 

./public/form.html: 
```html
<!--the action for this form should be to the root of whatever website this page is being hosted from. Also notice that the form's method is POST.

So what this means is when the user submits this form, we will submit this form via a POST request. And these form variables will be inside of the request headers.
-->

<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="minimum-scale=1.0, width=device-width, maximum-scale=1.0, user-scalable=no" />
  <meta charset="utf-8">
  <title>Fill out this Form</title>
    <style>
        label, input {
            display: block;
        }
    </style>
</head>
<body>
    <h1>Fill out this Form</h1>

    <form action="/" method="post">

        <label for="first">First name</label>
        <input type="text" id="first" name="first" required />

        <label for="last">Last Name</label>
        <input type="text" id="last" name="last" required />

        <label for="email">Email</label>
        <input type="email" id="email" name="email" required />

        <button>Send</button>

    </form>
</body>
</html>
```

./formserver.js
```javascript
var http = require("http");
var fs = require("fs");

http.createServer(function(req, res) {

	if (req.method === "GET") {
		res.writeHead(200, {"Content-Type": "text/html"});
	    fs.createReadStream("./public/form.html", "UTF-8").pipe(res);
	} else if (req.method === "POST") {

		var body = "";

		req.on("data", function(chunk) {
			body += chunk;
		});

		req.on("end", function() {

			res.writeHead(200, {"Content-Type": "text/html"});

			res.end(`

				<!DOCTYPE html>
				<html>
					<head>
						<title>Form Results</title>
					</head>
					<body>
						<h1>Your Form Results</h1>
						<p>${body}</p>
					</body>
				</html>

			`);


		});


	}	

}).listen(3000);

console.log("Form server listening on port 3000");


```


## Node Package Mananger
We are going to learn how to use the Node.js communities modules that will help us build web servers rapidly.

Example1: Installing node-dev
Node-dev is a development tool for Node.js that automatically restarts the node process when a file is modified.

In contrast to tools like supervisor or nodemon it doesn't scan the filesystem for files to be watched. Instead it hooks into Node's require() function to watch only the files that have been actually required.
```
sudo npm install -g node-dev
```

Example2: Installing jshint
JSHint is a community-driven tool that detects errors and potential problems in JavaScript code. Since JSHint is so flexible, you can easily adjust it in the environment you expect your code to execute. JSHint is open source and will always stay this way.
```
sudo npm install -g jshint
```
Note: to prevent jshint complaining about ES6 add this line at the top of your js file: 
/* jshint esnext:true */

Example3: file servers with httpster 
Simple http server for quick loading of content.

```
sudo npm install -g httpster
```

## Web Servers
### The package.json file 
When you want to do more than just serve static files, you will need to choose a framework that supports more functionality. In this next chapter, we are going to be taking a look at the Express framework. Express is a very popular framework for developing web server applications. 

Express is the most popular node.js framework. It was inspired by Sinatra, a rails based web server framework and express also represents the e in the MEAN stack. Express is usually the first framework that node.js developers are introduced to. You are highly likely to come across a web server application built in Express or a project with Express as an requirement in your day to day work as a node.js developer. 

We are going to focus on getting our project started by creating a package.json.

A package.json file is a manifest that contains information about our app. It will allow us to easily distribute our application code without having to worry about distributing all the dependencies as well. 

Let's go to our Terminal and under our Terminal I can actually create a package.json with an npm tool, npm init. So, npm init will start to build this package.json file for me.

```
npm init
```
You can actually get in here and add nodes and make changes to this package.json as your project continues to grow and develop.

It contains meta information about our project, but the package.json also does something very important. It will keep track of our projects dependencies. For this project we're going to be using express and along with express, we're going to be using a couple other modules to help us with the development of our application. 

Let's install modules:
```
npm install express --save 
```
The --save flag will add this dependency to my package.json file so when I installed express, I will have a reference to express in my manifest


Another node module that we are going to need with this project is cors. CORS stands for Cross Origin Resource Sharing and it's going to allow us to open up our api so that it is accessible by other domains.
```
sudo npm install cors --save
```

When a form is posted to a web server, that post is usually url encoded. Sometimes it can be encoded as json, so one of the tasks that you have to do is parse the form variables. What we want to do is have a node module do that for us and the node module that we're going to use is called body-parser.
```
sudo npm install body-parser --save
```


Let's go out to our files and I'm going to delete the node_modules folder. I'm simply going to Move it to the Trash.

So now, I do not have the dependent packages that I need installed. This is exactly how we are going to pass our files around. If I we're to publish this project on github or send this project to you in a zip via email, I can't send all the dependencies. They are way too big. But I do have a package.json file and that package.json file has a listing of all the dependencies that you would need to install to make this application work. If you were to receive these files, the first thing that you would do in the terminal, I'll go ahead and clear that leftover terminal text.

So, now we do not have any packages locally installed. What we can do is run an npm install. Now, I'm not specifying a package name or anything else. When I run an npm install, npm will take a look at my package.json file and it will go ahead and install all of the dependencies at once. Simply running an npm install has installed all of the required dependencies at once.

```
npm install
```

To remove a local package: 
```
npm remove package_name --save
```
the --save will also remove the package from my manifest: package.json 


### Intro to Express
add a public folder: 
index.html
```html
<!DOCTYPE html>
<html>
<head>

    <meta name="viewport" content="minimum-scale=1.0, width=device-width, maximum-scale=1.0, user-scalable=no"/>
    <meta charset="utf-8">

    <title>Skier Dictionary</title>

    <link rel="stylesheet" href="/css/style.css"/>

</head>
<body>

    <h1>Skier Dictionary</h1>
    <dl></dl>
    <p>Dictionary Empty</p>

    <form>
        <input type="text" id="term" name="term" placeholder="new term..." required />
        <input type="Text" name="defined" id="defined" placeholder="new definition..." required />
        <button>Add Term</button>
    </form>

    <script src="/js/jquery.min.js"></script>
    <script src="/js/dictionary.js"></script>

</body>
</html>
```

css: style.css
```css
html, body {
    height: 100%;
}

body, h1 {
    margin: 0;
    font-family: Arial;
}

h1 {
    margin: 10px 0;
    color: darkblue;
}

body {
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
    -webkit-box-orient: vertical;
    -webkit-box-direction: normal;
    -webkit-flex-direction: column;
        -ms-flex-direction: column;
            flex-direction: column;
    -webkit-box-pack: start;
    -webkit-justify-content: flex-start;
        -ms-flex-pack: start;
            justify-content: flex-start;
    -webkit-box-align: center;
    -webkit-align-items: center;
        -ms-flex-align: center;
            align-items: center;
}

dl {

    border: double;
    padding: 1em;
    display: block;
    width: 80%;
}

dl + p {
    display: none;
}

dl:empty {
    display: none;
}

dl:empty + p {
    color: red;
    display: block;
}

dt:hover {
    color: red;
    cursor: pointer;
}

dt {
    font-family: fantasy;
    font-size: 1.5em;
    color: darkslateblue
}

dd {
    font-family: verdana;
    margin: 0 0 10px 55px;
    font-size: 1em;
}

form {
    background-color: darkslateblue;
    color: white;
    padding: 1em;
    width: 80%;
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
    -webkit-justify-content: space-around;
        -ms-flex-pack: distribute;
            justify-content: space-around;
}

input, button {
    margin: 0 1em;
    padding: .5em;
}

input:first-of-type {
    -webkit-box-flex: 1;
    -webkit-flex-grow: 1;
        -ms-flex-positive: 1;
            flex-grow: 1;
}

input:last-of-type {
    -webkit-box-flex: 4;
    -webkit-flex-grow: 4;
        -ms-flex-positive: 4;
            flex-grow: 4;
}

button {
    -webkit-box-flex: 1;
    -webkit-flex-grow: 1;
        -ms-flex-positive: 1;
            flex-grow: 1;
    background-color: transparent;
    outline: none;
    border: solid 1px;
    color: white;
}

button:hover {
    color: yellow;
}
```

js: dictionary.js
```javascript

$(document).ready(function () {

    $.getJSON('/dictionary-api', printTerms);
    $('form').submit(function (e) {
        e.preventDefault();
        $.post('/dictionary-api', {term: $('#term').val(), defined: $('#defined').val()}, printTerms);
        this.reset();
    });

});

function printTerms(terms) {
    $('body>dl').empty();
    $.each(terms, function () {
        $('<dt>').text(this.term).appendTo('body>dl');
        $('<dd>').text(this.defined).appendTo('body>dl');
    });
    $('dt').off('dblclick').dblclick(function() {
        $.ajax({
            url: '/dictionary-api/' + $(this).text(),
            type: 'DELETE',
            success: printTerms
        });
    });
}

```

and also add the jquery.min.js (download it)

So the public folder contains the client side files for the site that we are going to build. So, if I actually just open up Index.html using the file API, you can see that I have a Skier Dictionary HTML page, but it doesn't look very pretty, and that is because our style sheets are not being loaded.

create a new file outside the public directory: 
app.js
```javascript

var express = require("express");

var app = express();
/*
Node.js knows to go look in the Node Modules folder to find the Express package. Now the next thing I need to do is create an application instance, and I can use the Express function to do that. var app will be my app instance, and invoking the Express function will create a new instance of an Express application. So now that I have a new instance of an Express application, I can add middleware to this application. You can think of middleware as being customized plugins that we can use with Express to add functionality to our application.
*/

/*
I'm going to add a custom piece of middleware that I can use to log each request.
I will use the app.use function to do so. Now, what we add to the app.use function is actually a callback function that will be invoked. So whenever we have a request, the app will use our custom piece of middleware. So whenever we have our request, now the application will first use our middleware function, and then it will proceed on to the express.static middleware.
*/
/*
So, each piece of middleware is a function that has three arguments: the request, the response, and the next function that you will invoke once you are finished. So what we're doing here is we are adding some functionality to our pipeline. Meaning that whenever we have a request, that request is going to trickle down through all of these app uses statements, until we find and return a response. So I am just going to use this function to log details about the request before returning the response.
*/
app.use(function(req, res, next) {
	console.log(`${req.method} request for '${req.url}'`);
    next();
    /*
    Now after we log these details to our terminal, we want to still serve the request.

    Our requests are presently being served in the next piece of middleware under express.static. So in order to tell our application to move on to the next piece of middleware in the pipeline, we need to invoke this next function. If we do not invoke this next function, we will never send a response back, and our application will technically not work.
    */
});

/*
The piece of middleware that we want to use is a static file server that comes with Express. Express.static will invoke the static file server that comes with Express, and we're going to add it to our app pipeline as a piece of middleware. Now, this function needs to take in the name of the directory where we would like to serve static files from. That directory is ./public. So I add a path to the public directory there, and now if we are making requests for any static files that are found under that directory, they will get served.
*/


app.use(express.static("./public")); //express static middleware


app.listen(3000);

console.log("Express app running on port 3000");

/*
 And then the next thing that I'm going to do is just go ahead and also export my app module.

Now, you don't need to do this for this Express app to run, but it's always a good idea. If I export this application instance as a module, that means I can include this application instance in other files. Later on, we are going to be including this application in the files that we will use to test it, so it's not a bad idea to add that now. 
*/
module.exports = app;
```

Now the dictionary-api is a 404 or bad request because we have not set up a route for dictionary-api. We are going to do that in the next lesson. 

### Express routing and CORS
In the last lesson, we created the Express app and we added the Express static middleware to serve the files that we have in the public folder statically. 

```javascript
var express = require("express");
var cors = require("cors");
var app = express();

var skierTerms = [
    {
        term: "Rip",
        defined: "To move at a high rate of speed"
    },
    {
        term: "Huck",
        defined: "To throw your body off of something, usually a natural feature like a cliff"
    },
    {
        term: "Chowder",
        defined: "Powder after it has been sufficiently skied"
    }
];


app.use(function(req, res, next) {
	console.log(`${req.method} request for '${req.url}'`);
	next();
});

app.use(express.static("./public"));

/*
Now, our dictionary api works great, but it has some limitations. The dictionary api, by default, is only allowed to serve this data to requests made for it from the same domain name. So our application works because our application is running on local host 3000 and our dictionary is also running on local host 3000. But what happens if our client application is running from skierdictionary.com and our dictionary api is running from skierterms.com? That means that this data, we would not be able to serve to a different domain name.

So we can only serve this data to the exact same domain. Now, we've already installed a module that will help us get around that.

Cors stands for "cross origin resource sharing," and this specific module is a piece of middleware that we can add to our Epress pipeline to solve this problem. 

Cors is a function and it's a function that will return some middleware. 
*/

app.use(cors()); //So, now we've added cross origin resource sharing to our application. This means that any domain can make a request for our dictionary api.

/*
The app.get function will set up a get route for me.

The first argument that it takes is the location for that route, so this is going to be at /dictionary-api. And the second argument is going to be the function that will actually handle any requests for that specific route. This function will take in our request object and our response object. Now these are the same request and response objects that we used with the http server, but they've been powered up by Express. Express has decorated these objects, has added some functionality to them to make things easy for us.

For instance, the response object now has a json function. In the response, json function will simply take a json object, like our skiier terms and automatically handle stringifying it, and setting up the headers to reply with a json response. 
*/
app.get("/dictionary-api", function(req, res) {
	res.json(skierTerms);
});

app.listen(3000);

console.log("Express app running on port 3000");

module.exports = app;
```

### Express post bodles and params 

Adding POST and DELETE routes:
```javascript
/*
The bodyParser is middleware that will help us parse the data that is posted to this API. So if we post data from my rest application, it will send data as .json. If we actually fill out a form in a web browser and POST the data, that data will be sent to us URL-encoded.

So we need to get in there and parse that POST data so that we can use those variables very neatly.
*/
var express = require("express");
var cors = require("cors");
var bodyParser = require("body-parser");
var app = express();

var skierTerms = [
    {
        term: "Rip",
        defined: "To move at a high rate of speed"
    },
    {
        term: "Huck",
        defined: "To throw your body off of something, usually a natural feature like a cliff"
    },
    {
        term: "Chowder",
        defined: "Powder after it has been sufficiently skied"
    }
];
/*
So I'm going to add an app.use to add the bodyParser and the first type of body that we want to parse are .json bodies. So if we have data sent to our API as .json, we will parse that data. And also we're going to use the bodyParser to make sure if the body data was sent URL-encoded that we are parsing that as well.
*/
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false })); // You will only need to set extended to true if you have large amounts of nested POST data to parse.

/*
So now that we've parsed this data, what happened? It just means that we have gone in and parsed all the variables that are posted to this application, and placed them neatly on the request object. 
*/

app.use(function(req, res, next) {
	console.log(`${req.method} request for '${req.url}' - ${JSON.stringify(req.body)}`);
	next();
});

app.use(express.static("./public"));

app.use(cors());

app.get("/dictionary-api", function(req, res) {
	res.json(skierTerms);
});

app.post("/dictionary-api", function(req, res) {
    skierTerms.push(req.body);
    res.json(skierTerms);
});

app.delete("/dictionary-api/:term", function(req, res) { // And right below my POST route I'm also going to add a DELETE route. And if you send a term to dictionary-api/ whatever term you send, I can set that up as a routing variable with a colon. So this means that I've just created a routing parameter called "term". The value of this variable will be whatever is found in the route after dictionary-api on a DELETE request.
    skierTerms = skierTerms.filter(function(definition) {
        return definition.term.toLowerCase() !== req.params.term.toLowerCase();
    });
    res.json(skierTerms);
});

app.listen(3000);

console.log("Express app running on port 3000");

module.exports = app;

//So at this point, our application seems to work. We can display dictionary terms, we can add new terms, and we can delete the terms. And our client app is getting these terms, posting these terms, and deleting these terms to our web server application via an API.
```

## Web Sockets 
### Creating a WebSocket server
Web Sockets are a wonderful addition to the HTML5 Spec. They allow for a true two way connection between the client and the server. Web Sockets use their own protocol to send and receive messages from a TCP server.

With Web Sockets, clients can connect to the server and leave a two way connection open. Through this connection, clients can send data that are easily broadcasted to every open connection. So the server is able to push data chain changes to the client using Web Sockets. Web Sockets are not just limited to the browser. Any client can connect to your server including native applications. Now instead of HTTP, Web Sockets use their own protocol. Settinng up a Web Sockets from scratch on your web application can be a little tricky You need a TCP Socket server and a HTTP proxy.

Install a websocket module: WS
```
sudo npm install ws --save
```

TO BE CONTINUED!!

## Automation with npm scripts
Npm also provides us a way to automate running, testing, debugging our applications or, really, running any Unix or DOS commands. A few of these scripts are quite important because they are commonly used by developers in infrastructure tools to automatically install dependencies, run tests,or even start our app.

package.json: 
```javascript
  "scripts": {
    "prestart": "grunt",
    "start": "node app",
    "predev": "grunt",
    "dev": "open http://localhost:3000 & node-dev app & grunt watch"
  },
  ```