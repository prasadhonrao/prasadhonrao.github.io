---
title: JavaScript Functions Part 1 - Function Declaration
categories: 
  - javascript
tags:
  - javascript
  - functions
comments: true
classes: wide
---

JavaScript is one of the most hated programming language amongst the developer community. There is infinite number of articles on the internet which highlights bad parts of the language. But the fact is that it’s still the most powerful language, thanks to the web. Though primarily introduced as client side programming language, JavaScript found its footprint on the server side as well with framework like [node.js](http://nodejs.org/ "NodeJS"). 

Functions are core part of the language and there are different ways it can be declared in JavaScript program including Function declaration, Function Expression, Anonymous Functions etc. However each of these option has some pros and cons associated with it. Thus, a series of short posts explaining JavaScript functions, different ways to define and its associated pros and cons.

Let’s start with Function Declaration.

## Function Declaration

Also known as _Function Statement_ is the oldest way of defining a function in JavaScript. It follows similar function declaration syntax as other programming languages like C, C++, C#, Java etc.

Code listing shown below defines a `logMessage` function, which logs the value of input parameter `message` to console.

{% highlight javascript %}

function logMessage(message){
    console.log(message);
}
logMessage("JavaScript is my favorite language.");

{% endhighlight %}

In the function declaration syntax, you must provide function name [`logMessage` in this case] during its declaration. If you skip function name, it becomes an _anonymous function_ which will cover later in this series.

### Trap 1 – Hoisting

In traditional programming languages like C# or Java, you need to define a function first before you can call it. JavaScript however works differently. Let’s update our code listing and call the `logMessage` function before its declaration.

{% highlight javascript %}
logMessage("JavaScript is my favorite language.");
function logMessage(message){
    console.log(message);
}
{% endhighlight %}


If you execute the code, program will not throw any exception and will simply log the message to the console. Now, how is that even possible? Enter function hoisting.

**Function hoisting** is the process in which JavaScript runtime hoists all the functions declared using function declaration syntax at the top of JavaScript file. So internally, even though you have called `logMessage` function before its declaration, JavaScript engine internally hoisted it at the top, so call to `logMessage` function gets executed without any error.

### Trap 2 – Readability
Function declaration syntax and dynamic nature of JavaScript language can sometimes impacts program readability. Again, let the code do the talking.

{% highlight javascript %}
function print(input) {
    console.log("Print was called with value " + input);
}
print(10);
function print(input) {
    console.log("Redefining print with value " + input);
}
print(20);
{% endhighlight %}


If you read above program, you would think that it will log messages as shown below

** TODO - Add Image

Unfortunately that is not the case, and again it’s related with function hoisting trap we discussed earlier. All the functions declared using function declaration syntax gets hoisted, so second `print` function overwrites first `print` function, so when you call `print` function with values 10 and 20 respectively, it will always get the overridden function and display the output as shown below.

** TODO - Add Image

So keep in mind, what you read and think is not what you always get in JavaScript. :)

Declaring functions using function expression syntax helps us to resolve this issue. We will cover Function Expression in next article.

Namaste.