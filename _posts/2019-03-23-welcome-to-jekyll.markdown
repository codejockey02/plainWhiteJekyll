---
layout: post
title:  "Asynchronous Adventures with Node.js"
date:   2019-06-17 17:45:00 +0530
categories: Javascript NodeJS Asynchrony Promises Callbacks
---
Node.js is a Javascript runtime and it is asynchronous in nature(through event loops). While Asynchronous programming comes with various features like faster execution of programs, it comes with a cost too i.e. usually it is a little bit difficult to program when compare to Synchronous programming.
Asynchronous vs Synchronous Programming
If we code in a Synchronous manner, the code runs line by line i.e it will not go to the next line until the execution of one line gets finished completely. The main disadvantage is that it unnecessarily stops the execution of that code which is independent of the code that is executing currently. Ultimately, it slows down the execution of the program.

Asynchronous is the exactly opposite of what Synchronous Programming does. The code in asynchronous manner executes without depending on the execution of the previous lines. The advantage is that the program doesn’t have to wait for the execution of the further code while the previous code is currently being executed. It ultimately increases the program efficiency and also the throughput.

In this article, I’ll discuss about the different ways through which we can introduce Asynchronous manner in our code and the difficulties that one usually face.

1. Using Callback functions
Callbacks are the functions that are called when a particular execution gets completed. For example, if you are trying to read/write a file and then you have to pass some parameters of a file into a function, then that function can be introduced as a callback which will be automatically called when the file I/O gets completed. It makes the efficiency of a program much better as it doesn’t have to wait for any function to get completed. Here is an example on how to use callbacks.

```javascript
var fs = require("fs");

fs.readFile('input.txt', function (err, data) {
   if (err) return console.error(err);
   console.log(data.toString());
});

console.log("That's Callback.");
```
The output of the snippet will be
```
That's Callback.
"Data in the File"
```
The first line that gets printed will be “That’s Callback” because of the asynchrony and in the meantime, the reading of file gets completed and then callback function is called.

If we do the same code without Callbacks then it would appear like this:
```javascript
var fs = require("fs");

fs.readFile('input.txt')
console.log(data.toString());
console.log("That's Not Callback.");
```
The output will be
```
Undefined
That's Not Callback
```
Now, we have seen the perks of using Callbacks. Let’s discuss the drawbacks.

The major drawback of using Callbacks is the CALLBACK HELL or some people also call it as the Christmas Tress Problem as it includes great amount of Nesting of our code. Suppose, after reading the file and passing the data to string we want to pass that string into another function (callback2) and from that function to another function (callback 3). Real Life problems can involved these type of issues. This situation is called as a Callback Hell and it is how it looks like:
```javascript
var fs = require('fs')
fs.readFile('input.txt', function (err, data) {
   if (err) 
       return console.error(err);
   else
       fs.writeFile('output.txt', function(err, data) {
           if(err)
               return console.error(err);
           else
               fs.readFile('new.txt', function(err, data) {
                   if(err)
                       return console.error(err);
                   else
                       console.log(data.toString());
});
console.log("That's a Callback Hell");
```
2. Using Promises
Promise is basically an object which holds the eventual results of an asynchronous function. It simply promise you to hold the results of the async function that is being executed. A Promise can be in one of the 3 states which are Pending State, Fulfilled State or Rejected State. When the asynchronous function is being executed, the promise is in Pending State. When the operation is successful, it comes to the Fulfilled State and if some error comes in the way, it goes into the Rejected State.
Now, let’s see how to create Promises.

```javascript
const p = new Promise((resolve, reject)=>{
    //Some Asynchronous Operations
    resolve("Promise Resolved");
});
//Consuming the Promise
p.then(result => console.log(result));
```
The Promise constructor takes two arguments with it, resolve and reject. Resolve is the function which tells what to do if the Asynchronous operation is fulfilled and reject function tells about the Errors, if there is any.

For consuming promise, there are two functions then and catch. In the above snippet, the argument result takes the value “Promise Resolved”. Therefore, the output will be:
```
Promise Resolved
```
If there is some error, then we need to catch it.
```javascript
const p = new Promise((resolve, reject)=>{
    //Some Asynchronous Operations
    reject("Err....error");
});
//Consuming the Promise
p.catch(err => console.log(err.message));
```
The output of this code will be
```
Err....error
```
That’s how we consume promises using then and catch.

Now, let’s convert the above Callback Hell code into Promises.
```javascript
var fs = require('fs');
function readfile() {
    return new Promise((resolve, reject) => {
        fs.readFile(('input.txt') => {
            resolve('File 1 Read Successful');
        });
    });
}
function writefile() {
    return new Promise((resolve, reject) => {
        fs.writeFile(('output.txt') => {
            resolve('File 2 Write Successful');
        });
    });
}
function readfile2() {
    return new Promise((resolve, reject) => {
        fs.readFile(('new.txt') => {
            resolve('File 3 Read Successful');
        });
    });
}
//Consuming the Promises now
readfile()
    .then((result) => {
        console.log(result);
        writefile();
    })
    .then((result) => {
        console.log(result);
        readfile2();
    })
    .then((result) => {
        console.log(result);
    });
```
The output will be
```
File 1 Read Successful
File 2 Write Successful
File 3 Read Successful
```
3. Using Async/Await Functions
We can make the Asynchronous Programming even simpler by using Async/Await functions. As we all know that writing Synchronous code is much easier than writing Asynchronous one. So, the Async and await functions lets you write the Asynchronous code in a Synchronous manner.

Actually, these functions are build on top of Promises. When the Javascript engine runs the code, it converts it into the Promise type and then executes it asynchronously. Here is a code snippet to get a better understanding of Async and await.
```javascript
//Database call is an Asynchronous function
async function fetchingFromUserDatabase() {
    const check = await User.findOne({username:'Priyesh'},{pwd:1});
    console.log("My Password is: "+check.pwd);
}
fetchingFromUserDatabase();
```
This is written purely in Synchronous manner but it is an Asynchronous Code. The first rule is, Async and await go hand in hand, we cannot use one without the other. Therefore, we can use Await keyword only in an async function. Await represents that this line is executing asynchronously, and hence the line depending on it will be executed once the result is ready.

Therefore the output of the above code will be:
```
My Password is: AsynchronousAdventuresCompleted
```
So, these are the ways through which we can execute asynchronous approach in Node.js. If you have any queries, you can contact me at priyeshsaraswat9@gmail.com
