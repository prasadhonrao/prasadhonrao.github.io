---
title: JavaScript Functions Part 2 - Function Expression
categories: 
  - javascript
tags:
  - javascript
  - functions
comments: true
classes: wide
---

Welcome to JavaScript Function series. 

In [part 1](https://www.iamprasad.com/blog/javascript-functions-part-1-function-declaration/index.html "JavaScript Functions Part 1 – Function Declaration"), we discussed Function Declaration syntax in JavaScript and few traps associated with it. In this article we will discuss JavaScript Function Expression.

## Function Expression

A function expression has similar syntax as function declaration except that function value is assigned to a variable name. Let's take a look at an example -

```javascript
var log = function logMessage(message) {
    console.log(message);
}
log("This is a function expression");
```

Once you define a function using this syntax, the function name [`logMessage` in this case] becomes obsolete and the function can be called only using the assigned variable name [`log`]. 

So you might be thinking, JavaScript runtime must be doing function hoisting in this case as well and you can call the function first and define it later?  Well, your assumption is partially correct. JavaScript engine does perform function hoisting in this case, however since the function was assigned to a variable value, it hoist the function variable instead of complete function definition. So JavaScript runtime re-implements the code as shown in below code snippet

```javascript
var log;
log = function logMessage(message) {
    console.log(message);
}
log("This is a function expression");
```

Note again, it doesn’t hoist function definition completely. It just hoists the assigned variable and initializes it where function was initially defined. So in this case, you cannot call the function unless its explicitly defined earlier. Doing so will result into a runtime exception `undefined is not a function` since we are trying to call a function which is not defined.

```javascript
var log;
log("JavaScript is my favorite language"); // error
log = function logMessage(message){
	console.log(message);
}
```

## Readability trap? – Not any more
Let’s take a look at readability trap we discussed in function declaration example in earlier post and redefine `print` functions using function expression syntax.

```javascript
var print = function (input) {
    console.log("Print was called with value " + input);
}
print(10);
var print = function (input) {
    console.log("Redefining print with value " + input);
}
print(20);
```

As mentioned earlier, JavaScript engine hoists the function variables and not the complete function definition, it re-implements above code as - 

```javascript
var print;
print = function (input) {
    console.log("Print was called with value " + input);
}
print(10);

//overrides print function declared earlier
print = function (input) { 
    console.log("Redefining print with value " + input);
}
print(20);
```

In this case, after calling `print` function `print(10)`, we are overriding `print` function definition, hence both the calls to `print` function will display output as shown below.

** TODO - Add Image

So that was a quick overview on function expression syntax in JavaScript. Coming up next is anonymous functions, stay tuned.

Namaste.