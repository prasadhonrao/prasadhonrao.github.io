---
title: JavaScript Functions Part 4 - Immediately Invoked Function Expression [IIFE]
categories: 
  - javascript
tags:
  - javascript
  - functions
comments: true
classes: wide
---

Welcome back to JavaScript Functions series. Earlier in the series we have covered - 

*   [Function Declaration](https://www.iamprasad.com/blog/javascript-functions-part-1-function-declaration/index.html "JavaScript Functions Part 1 – Function Declaration")
*   [Function Expression](https://www.iamprasad.com/blog/javascript-functions-part-2-function-expression/index.html "JavaScript Functions Part 2 – Function Expression")
*   [Anonymous Functions](https://www.iamprasad.com/blog/javascript-functions-part-3-anonymous-functions/index.html "JavaScript Functions Part 3 – Anonymous Functions")

In this article we will focus on IIFE.

## IIFE - What’s in the name?

Some people refer IIFE as _Self-executing anonymous functions_ or _Self-invoked anonymous functions_ and sometimes it gets totally confusing to follow these naming conventions. Just to be clear, all these names refer to same concept in JavaScript language. Many JavaScript people including me prefers to call it as IIFE. 

I hope by you now have a clear understanding that JavaScript language provides only two ways to define a function – using Function Declaration or Function Expression. Language features like anonymous functions,  IIFE doesn’t provide new syntax to define a new function, rather they provides additional benefits to your program like callback functions, closures, public and private scope etc.

Before we dive into what is an IIFE, we need to understand the concept of Module in JavaScript world.

## JavaScript Module

A module is a collection of logically grouped functions to perform expected work in your program. The primary purpose of these modules is to hide the implementation details of functions and provide an entry point [or API] to access these functions from outside. For example, you can define a `payroll` module within your program which can contain functions like `makePayment`, `calculateOverTime` etc. It is not necessary to expose the internal implementation details of these functions as it might not be useful [or even dangerous!] to the external program. 

In programming languages like C# or Java, a module can be defined using a class construct. Current version of JavaScript language doesn’t support class construct [but future will!], however you can still define a module using JavaScript function as shown below.  

```javascript
var payroll = function() {
    var makePayment = function () {
        console.log("In makePayment function");
    };
    var calculcateOverTime = function () {
        console.log("In calculateOverTime function");
    };
};
```

Note that `makePayment`, `calculateOverTime` functions are declared inside a `payroll` function. Since JavaScript support only function scope, variables and functions declared inside a function are accessible only inside the function. In order to provide an entry point to `payroll` module, we need to change the function definition as shown below

```javascript
var payroll = function() {
    var makePayment = function () {
        console.log("In makePayment function");
    };
    var calculateOverTime = function () {
        console.log("In calculateOverTime function");
    };
    return {
        MakePayment : makePayment,
        CalculateOverTime : calculateOverTime
    }
};
```

The `return` block in above function definition exposes both `MakePayment` and `CalculateOverTime` functions to external clients, which can be accessed as shown below 

```javascript
var payrollAdmin = payroll();
payrollAdmin.MakePayment();
payrollAdmin.CalculateOverTime();
```

With this approach you can even define a private variables or function within your module, which you can hide from external callers. In below code snippet, I have defined an `audit` function which is not exposed to the outside world.

```javascript
var payroll = function() {
    var audit = function(info) {
        console.log("Auditing information " + info)
    };
    var makePayment = function () {
        console.log("In makePayment function");
        audit("success");
    };
    var calculcateOverTime = function () {
        console.log("In calculateOverTime function");
        audit("success");
    };
    return {
        MakePayment : makePayment,
        CalculateOverTime : calculcateOverTime
    }
};
```

With this basic introduction of modules in JavaScript, let’s understand **Global Variables** and how to avoid them using IIFE.

## Global Variables
In many programming languages usage of global variables is considered as a bad practice. In JavaScript they are evil and if you notice any global variables in your JavaScript program, you should try to refactor the code to remove them.

In the `payroll` module / function defined earlier, we have accidentally created two global variables - `payroll` and `payrollAdmin`. In current example, since we know entire function definition we can ensure that global variables have no impact on function execution. However in complex and large scale JavaScript programs, if you create global variable like this, there are more chances that you override the definition of existing functions or module with your function. For e.g. if you refer external JavaScript file which already has `payroll` function in it and unfortunately it has been declared at global scope as well, your declared `payroll` function will override it during program execution, which may result into unexpected output. So, how can we eliminate global variables from our program? Enter IIFE.

## IIFE

An IIFE is a common JavaScript design pattern used by most popular libraries (jQuery, Backbone.js, Modernizr etc.) to place all library code inside of a local scope. It is just an anonymous function that is wrapped inside of a set of parentheses and invoked immediately.

A standard IIFE looks like this

```javascript
(function () {
    // Code goes here
}());
```

The advantage of the IIFE is that any variables declared inside it are inaccessible to the outside world. So how does that help us? The key is that an IIFE can have a return value just like any other function.

```javascript
var payroll = (function() {
    var audit = function(info) {
        console.log("Auditing information " + info)
    }
    var makePayment = function () {
        console.log("In makePayment function");
        audit("success");
    };
    var calculcateOverTime = function () {
        console.log("In calculateOverTime function");
        audit("success");
    };
    return {
        MakePayment : makePayment,
        CalculateOverTime : calculcateOverTime
    }
}());
```

As you can see now, we were able to use IIFE’s return value to provide access to `makePayment` and `calculateOverTime` functions without providing their internal implementation details.

So that is all I wanted to cover in this article. I hope it helped you to understand the JavaScript module and how you should encapsulate it using IIFE pattern to provide public / private scope for internal implementation.

Thanks for reading.

Namaste.