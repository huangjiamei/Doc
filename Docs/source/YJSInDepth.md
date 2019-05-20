YJSInDepth
==========

#Layout of a YJS Source File

##Overview
YJS source files can contain an arbitrary number of **import statements** and a **contract definition**.

##Import Statements
YSJ supports import statements that are very similar to those available in JavaScript(from ES6).
At a global level, you can use import statements of the following form:

~~~
import "filename";
~~~
###Contents
This statement imports all global symbols (modules) from "filename" (and symbols imported there) into the current global scope.
###Path
In the above, **filename** is always treated as a path with **/** as directory separator, **.** as the current and **..** as the parent directory.
To import a file **x.yjs** from the same directory as the current file, use **import "x.yjs"**.

##Contract Definition
###Demo
In the following example we document the title of the contract, which is for JSON processing.

~~~
contract ScoreAdder{
	//arg =  {"action":"main","arg":"[{\"score\":20},{\"score\":20}]"}
	export function main(arg){
		//JSON is a build-in object.
		var point = JSON.parse(arg);
		var s = 0;
		print(point[0].score);
		print(point.length);
		for (var i=0;i<point.length;i++){
			s+=point[i].score/1.0;
		}
		print("total score= "+s);
		return s;
	}
}
~~~

###Comments
Single-line comments(**//**) and multi-line comments(**/\*...\*/**) are possible.

~~~
// This is a single-line comment.
/*
 * This is a 
 * multi-line comment.
 */
~~~ 
 
###Structure
Contracts in YJS are similar to classes in Javascript. Each contract can contain a number of **variables**, **functions** and **events**.

####Variables
Variables in YJS are similar to variables in JavaScript(from ES6). There are **global variables** and **local variables**.

Global variables:

~~~
judge =  true;
~~~
Local variables:

~~~
var judge = "This is a string";
~~~

The types of variables are so-called value types because types will always be passed by value.

####Functions
Functions are the executable units of code within a contract. There are two types of funcions. Except for common functions, functions can be defined as exported functions by using **export**. 

Here are the differences between exported fuctions and common functions.

1. Only exported function can be exported to other contracts.
2. Exported function can have only one parameter and the type required to be "String".
3. The return type of exported function must be "String".
4. The build-in field, requester(the public key of requester), is only available in exported functions. But there is an exception. Requester is availabe in "onCreate()" function originally and this function can execute automatically.

####Events
Events in YJS are similar to events in Solidity.

The publisher define an event which contains an event-publish function, then publish it through function call.

The subscriber subscribe and handle this event.

There is an event demo as follows:

EventPuber.yjs

~~~
contract EventPuber{
	event abcEvent;
  	export function pub(arg){
		abcEvent(arg);
  		return "done!";
	}
}
~~~
EventSuber.yjs

~~~
contract EventSuber{
  	export function init(arg){
		YancloudUtil.subscribe("EventPuber","abcEvent",handler);
        print("Handler:"+handler);
	}
  	function handler(e){
        var ret = "ReceiveEvent:"+(e.type)+" "+(e.content);
		print(ret);
	}
}
~~~


##YJS Projects
Except for the single YJS source file which contains only one contract, There are **contract projects**.

Each contract project contains kinds of files which will be used during the execute of contract, such as **"mainfest.json"**, **".yjs"**, **".js"**, **".txt"**,**".jar"**,  ...

###"Manifest.json"
Each contract project also must have an **mainfest.json** file(with precisely that name) at the root of the project source set. The manifest file describes essential information about the constract to the YJS build tools, the YJS Engine.
####Manifest Structure
The manifest file is required to declare the following:

1.  **main**: the file to be executed.
2.  **type**: the type of contract, such as Data/Algorithm/...
3.  **builder**: the name of person who bulid this YJS project.
4.  **permissions**: TODO
5.  **pyDependences**: the needed python dependences.

####Manifest Demo

~~~
{
	"main": "contract.js",
	"type": "Data",
	"builder": "caihq",
	"permissions": [],
	"pyDependences": [
        {
            "name": "yjsexample",
            "modules": [
                {
                    "name": "sample"
                }
            ]
        }
    ]
}
~~~

###".jar"
Jar file achieved the **cross-language interoperatility**(including YJS-Java, YJS-Python, ... 
) through packing the Java class file or the Python file with the YJS file, then the contract can use the functions of Java and Python. 
####YJS-Java
Java class:

~~~
package your.own.package;
public class HelloWorld {
 public static int fun(String arg){
  return arg.length;
 }
 public int fun2(String arg){
  return arg.length;
 }
}
~~~


InvokeJava.yjs

~~~
contract InvokeJava{
 export function main(arg){
  var hello = new your.own.package.HelloWorld();
  return your.own.package.HelloWorld.fun(arg)+hello.fun2(arg);
 }
}
~~~

####YJS-Python
TODO





