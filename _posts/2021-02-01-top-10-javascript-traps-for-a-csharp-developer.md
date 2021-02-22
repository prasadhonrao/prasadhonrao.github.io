---
title: Top 10 JavaScript traps for a C# developer
categories:
  - programming
tags:
  - c#
  - javascript
# toc: true
# toc_label: "Traps"
# toc_icon: "cog"
classes: wide
---

If you are an experienced C# developer, coming into JavaScript world for application development, you will end up making few common mistakes. However some of the mistakes you would make are due to the basic differences between any strongly typed language [C#, Java etc.] and a dynamically typed language [JavaScript, Python etc]. Although dynamic feature was added to C# version 4.0, its initial design was based on static typing.

Note, I am primarily a .NET developer and have experience of developing web applications using JavaScript, and I admit that I made these mistakes when I started learning JavaScript. I spent good amount of time analyzing the root cause of the issues, just to realize that some of the mistakes were really due to my experience in C# language and assumption that JavaScript code would work exactly. I was wrong however. So in this article, I will cover top 10 JavaScript traps for a C# developer so that they don’t make the same mistakes which I did.

I assume that you have basic experience with C# language, so that we can focus more on JavaScript language in this article. With that, let’s jump into the traps.

## 1. Equality Operator
Most common trap between the two languages is the equality operator, also known as comparison operator. Unlike C# which has one equality operator [==], JavaScript has two [== and ===]. 

Let's take a look at equality operator in C# first.

For predefined value types in C#, equality operator returns true if the values of its operands are equal, otherwise it returns false. Assuming you have basic understanding of C# language, below code snippet should be easy to understand.

{% highlight c# %}

    Console.WriteLine("10 == 10 ? " + (10 == 10)); // returns true
    Console.WriteLine("10 != 10 ? " + (10 != 10)); // returns false.
    Console.WriteLine("10 != 20 ? " + (10 != 20)); // returns true. 

{% endhighlight %}

For reference types other than string, equality operator returns true if its two operands refer to the same object.
In the code snippet below, `emp1` and `emp2` are two different `Employee` instances, hence comparing two different instances in such case would return a false value.

{% highlight c# %}

    var emp1 = new Employee();
    var emp2 = new Employee();
    Console.WriteLine("emp1 == emp2 ? " + (emp1 == emp2)); // returns false

{% endhighlight %}

In below code snippet, however, we have assigned `emp2` instance variable to `emp1`, so both the instances are now pointing to same memory location. So comparing two instance in such case would return a true result.

{% highlight c# %}

    emp1 = emp2;
    Console.WriteLine("emp1 == emp2 ? " + (emp1 == emp2)); // returns true

{% endhighlight %}

For the string type, == compares the values of the strings. In the code snippet below, strings "A" and "B" are not same, comparing these two strings would return a false result.

{% highlight c# %}

    Console.WriteLine("A == B ? " + ("A" == "B")); // returns false

{% endhighlight %}

In below code snippet, we have copied string "A" to `temp` variable and compared it with "A" string constant. Since both the strings contains same value, comparison in such case would return a true value. 

{% highlight c# %}

    var temp = string.Copy("A");
    Console.WriteLine("A == A ? " + ("A" == temp));

{% endhighlight %}

In JavaScript however, equality operator works differently. Let’s understand the difference in these two equality operators first.

**Equals(==)** 

If the two operands are of same datatype, equality operator returns true if operand values are same, false otherwise.

{% highlight javascript %}

    console.log('10 == 10 ? ' + (10 == 10)); // returns true
    console.log('"10" == "10" ? ' + ("10" == "10")); // returns true
    console.log('true == true" ? ' + (true == true)); // returns true

{% endhighlight %}

If the two operands are NOT of the same datatype, JavaScript converts the operands and then applies comparison. If either operand is a number or a boolean or a string, the operands are converted to numbers if possible. 

In below code snippets, string value "10", boolean value 'true' and 'false' gets converted into a number before the comparison takes place. All the comparison statement below returns a true value.

{% highlight javascript %}

    console.log('10 == "10" ? ' + (10 == "10"));
    console.log('1 == true ? ' + (1 == true));
    console.log('0 == false ? ' + (0 == false));
    console.log('0 == "" ? ' + (0 == ""));
    console.log('0 == " " ? ' + (0 == " "));

{% endhighlight %}

If both operands are objects, then JavaScript compares internal references which are equal when operands refer to the same object in memory.

{% highlight javascript %}

    var x = { name: 'Prasad' };
    var y = { name: 'Prasad' };
    var z = x;

    console.log(x == y); // returns false
    console.log(x == z); // returns true

{% endhighlight %}

**Strict Equal (===)**

Returns true if the operands are strictly equal with no type conversion.

In below code snippet, both the operands are of same data type with same value, all the comparison statements would return true result.

{% highlight javascript %}

    console.log('10 === 10 ? ' + (10 === 10));
    console.log('"20" === "20" ? ' + ("20" === "20"));
    console.log('true === true ? ' + (true === true));

{% endhighlight %}

Below code statement would return false result since the data type of the operands are different.

{% highlight javascript %}

    console.log('10 === "10" ? ' + (10 === "10"));
    console.log('10 === "20" ? ' + (10 === "20"));
    console.log('1 === true ? ' + (1 === true));
    console.log('0 === false ? ' + (0 === false));
    console.log('0 === "" ? ' + (0 === ""));
    console.log('0 === " " ? ' + (0 === " "));
    console.log('"" === " " ? ' + ("" === " "));

{% endhighlight %}

## 2. Variable Scope

C# allows programmer to declare a variable at different locations, which determines its scope. Variables that are defined at the class level are available to any non-static method within the class. Variables declared within a method's code block are available for use by any other part of the method, including nested code blocks. And finally, if you declare a variable within a block construct such as an `if` statement, that variable's scope is only until the end of the block. 

In the code listing below variable `favoriteCity` is declared at class level, so it is accessible to all the methods in the class `VariableScope`. Considering its a static variable, it can be accessed inside a static or an instance method, however it wont be accessible using a class instance.

Variables declared at class level can be overridden in the functions. In below example, `Main` method overrides variable `favoriteCity` and sets it to "New York". Since the class level variable is accessible to all the methods, `PrintFavoriteCity` function can access it. Any local variable declared in `Main` method or `PrintFavoriteCity` won't be accessible outside method definition.

Finally, we have declared a `welcomeMessage` variable inside a `if` block. The scope of this variable is only within the `if` block and it wont be accessible outside the block, within `Main` method or `PrintFavoriteCity` method.

{% highlight c# %}

    class VariableScope
    {
        private static string favoriteCity = "London";
        static void Main()
        {
            Console.WriteLine("Your favorite city is {0}", favoriteCity);
            favoriteCity = "New York";
            PrintFavoriteCity();

            if (favoriteCity.Equals("New York"))
            {
                    var welcomeMessage = "Welcome New Yorker";
                Console.WriteLine(welcomeMessage);
            }
        }

        static void PrintFavoriteCity()
        {
            Console.WriteLine("Your favorite city is {0}", favoriteCity);
        }
    }

{% endhighlight %}

JavaScript has two scopes: **global** and **local**. A variable that is declared outside a function definition is a global variable, and its value is accessible and modifiable throughout your program. A variable that is declared inside a function definition is local. It is created and destroyed every time the function is executed, and it cannot be accessed by any code outside the function. JavaScript does not support block scope.

In the code listing below, `favoriteCity` variable is declared outside function definition, hence its scoped as global and can be accessed inside any function. Variable `favoriteIndianCity` is declared inside `PrintFavoriteCity` function, so its a local scope variable and cannot be accessed outside function definition. Finally we have declared a `welcomeMessage` variable inside an `if` block. Since JavaScript does not support block scoped variable, `welcomeMessage` variable can be accessed anywhere within the containing function. 

{% highlight javascript %}

    var favoriteCity = "London";
            
    function PrintFavoriteCity() {
        console.log(favoriteCity);

        var favoriteIndianCity = "Pune";
        console.log(favoriteIndianCity);
        
        if (true) {
            var welcomeMessage = "Hello dear traveler!";
            console.log(welcomeMessage);
        }

        welcomeMessage = "Hello dear traveler, welcome back! ";
        console.log(welcomeMessage);
    }

{% endhighlight %}

## 3. For each 

In C# [foreach](http://msdn.microsoft.com/en-us/library/ttw7t8t6.aspx) statement is used to repeat a group of embedded statements for each element in an array or a collection which implements [IEnumerable](http://msdn.microsoft.com/en-us/library/system.collections.ienumerable.aspx) or [IEnumerable\<T\>](https://msdn.microsoft.com/en-us/library/9eekhta0.aspx) interface. 

Most of the collection classes in C# like [Array](http://msdn.microsoft.com/en-us/library/vstudio/system.array), [List](http://msdn.microsoft.com/en-us/library/6sh2ey19(v=vs.110).aspx), [Dictionary](http://msdn.microsoft.com/en-us/library/xfhwa508(v=vs.110).aspx), [HashSet](http://msdn.microsoft.com/en-us/library/bb359438(v=vs.110).aspx) etc implement these interfaces, so iterating over these collection using `foreach` statement is a common practice.

**Iterating over string array collection**

{% highlight c# %}

    var rainbowColors = new[] { "Red", "Orange", "Yellow", "Green", "Blue", "Indigo", "Violet" };
    foreach (var rainbowColor in rainbowColors)
    {
        Console.WriteLine(rainbowColor);
    }

{% endhighlight %}

**Iterating over specialized collection - StringCollection**

{% highlight c# %}

    var cities = new StringCollection { "Pune", "London", "New York", "Mumbai" };
    foreach (var city in cities)
    {
        Console.WriteLine(city);
    }

{% endhighlight %}

**Iterating over Generic List Collection**

{% highlight c# %}

    var weekdays = new List<string>(){"Mon", "Tue", "Wed", "Thur", "Fri", "Sat", "Sun"};
    foreach (var weekday in weekdays)
    {
        Console.WriteLine(weekday);
    }

{% endhighlight %}

Unlike C#, JavaScript has three flavors of for each statements!.

**for each...in**

It is used to iterate a specified variable over all values of object's `properties`. For each distinct property, a specified statement is executed. However, `for each...in` is deprecated as the part of ECMA-357 ([E4X](https://developer.mozilla.org/en-US/docs/E4X)) standard. E4X support has been removed, but `for each...in` will not be disabled and removed because of backward compatibility considerations.

{% highlight javascript %}

    var sum = 0;
    var obj = {prop1: 10, prop2: 20, prop3: 30};

    for each (var item in obj) {
        sum += item;
    }

    print(sum); // prints "60", which is 10+20+30

{% endhighlight %}

**for...in**

It is used to iterates over property names in arbitrary order. Since the order is not consistent, this version should not be used to iterate over array elements.

{% highlight javascript %}

    let arr = [ 3, 5, 7 ];
    arr.foo = "hello";

    for (let i in arr) {
    console.log(i); // logs "0", "1", "2", "foo"
    }

{% endhighlight %}

**for...of**

It is used to iterates over property values in consistent order. This version is suitable to iterate over array like elements, however this feature is yet to be implemented in all the browsers and is part of ECMA6 proposal.

{% highlight javascript %}

    let arr = [ 3, 5, 7 ];
    arr.foo = "hello";

    for (let i of arr) {
    console.log(i); // logs "3", "5", "7"
    }

{% endhighlight %}

##  4. Switch statement
In C#, the switch statement is a control statement that selects a switch section to execute from a list of candidates. Each switch section contains one or more case labels followed by one or more statements. 

The following example shows a simple switch statement that has five switch sections. Each switch section has one case label, such as case 10, case 20 etc. Each case label specifies a constant value. The switch statement transfers control to the switch section whose case label matches the value of the switch expression.  If no case label contains a matching value, control is transferred to the default section, if there is one. If there is no default section, no action is taken and control is transferred outside the switch statement.

{% highlight c# %}

    Console.WriteLine("Enter your age");
    var age = int.Parse(Console.ReadLine());

    switch (age)
    {
        case 10:
        Console.WriteLine("Drink milk");
        break;
        case 20:
        case 30:
        Console.WriteLine("Take Latte or beer");
        break;
        case 50 - 10:
        Console.WriteLine("You can have Whiskey");
        break;
        case 50:
        Console.WriteLine("Take medicine");
        break;
        default:
        Console.WriteLine("Invalid input");
        break;
    }

{% endhighlight %}

JavaScript is a dynamically typed language. In other words, language doesn't enforce you to declare the type during variable declarations and you can easily change the data type later. As we discussed earlier, in regular `if` statement comparison, data type doesn’t matter unless you are using strict comparison that specifically check for data type.

{% highlight javascript %}

    y = 5;
    x = '5';
    if(x == y){
        console.log("they're the same"); // logs 'they're the same'
    }

{% endhighlight %}

The exception is case values in the switch statement. For the case to be a match, the data types must match. Think for it as switch statements including === (strict equal) instead of == (equal) for case comparisons.

{% highlight javascript %}

    y = 5;
    switch(y){
        case '5':
        console.log("y is 5"); // this won't log the message
    }

{% endhighlight %}

However, main difference in JavaScript switch statement is that, each section can contain an expression or a constant. Note, C# language accepts only constant value in switch expression. Below JavaScript code snippet demonstrates case expression. Note the `switch(true)` statement, which always execute in this case and the actual value of `input` is used in case expression.

{% highlight javascript %}

    var input = prompt("enter your age");
    switch (true) {
        case input > "10" && input < 20:
            console.log('Take Milk');
            break;
        case input > "21" && input <= 40:
            console.log("Take Beer");
            break;
        case input >= 41 && input <=50 :
            console.log("Take Whiskey");
            break;
        case input > 51:
            console.log("Take Medicine");
            break;
        default:
            console.log("Invalid input");
            break;
    }

{% endhighlight %}

## 5. Function overloading

In C#, function overloading is, when you have two or more methods with the same name but different signatures. At compile time, the compiler works out which one it's going to call, based on the compile time types of the arguments and the target of the method call.

In below code snippet, we have two forms of `Add` function and depending on the type and number of arguments, appropriate `Add` function gets called.

{% highlight c# %}

    static void Main()
    {
        Console.WriteLine("10 + 20 = " + Add(10,20));
        Console.WriteLine("10 + 20 + 30 = " + Add(10, 20, 30));
        Console.ReadLine();
    }

    static int Add(int number1, int number2)
    {
        return number1 + number2;
    }

    static int Add(int number1, int number2, int number3)
    {
        return number1 + number2 + number3;
    }

{% endhighlight %}

JavaScript does not support function overloading! Even though everything in JavaScript is an Object in itself, the language does not support some of the common object oriented language feature like function overloading.

So what happens if you declare two functions with the same name? Lets see it in action.

In the code block defined below, we have declared two `Add` functions which takes different number of parameters. JavaScript unfortunately overrides the `Add` function defined earlier using 2 parameters with the `Add` function defined later using 3 parameters, so for a calling application only one `Add` function is available.

{% highlight javascript %}

    function Add(num1, num2) {
    	return num1 + num2;
    }

    function Add(num1, num2, num3) {
	    return num1 + num2 + num3;
    }

    var Add2 = Add(10, 20); // num3 gets set to undefined
    console.log(Add2);
    var Add3 = Add(10, 20, 30);
    console.log(Add3);

{% endhighlight %}

So the question is, what happens when we call `Add` function with 2 arguments, when its signature contains three arguments. Well, in above case, parameter `num3` gets initialized to `undefined`, which is our next trap in the list!

##  6. undefined function parameter

In C#, once you define a function with a given number of parameters, the calling code has to pass same number of arguments, unless any of the parameters are set as optional. For example, `WelcomeUser` function define in below code takes two parameters – `userName` and `welcomeMessage`. 

{% highlight c# %}

    static void WelcomeUser(string userName, string welcomeMessage = "Welcome")
    {
        Console.WriteLine("{0} {1}!", welcomeMessage, userName);
    }

{% endhighlight %}

The `welcomeMessage` parameter was declared as an optional parameter, so the calling code may not necessarily pass its value, in which case its default value ‘Welcome’ will be considered during function execution. If the calling code passes `welcomeMessage` argument, it will override its default value while calling the function.

{% highlight c# %}

    static void Main(string[] args)
    {
        WelcomeUser("Prasad");
        WelcomeUser("Colin", "Good Morning");
        // WelcomeUser(); // this won't compile as userName is mandatory parameter
    }

{% endhighlight %}

JavaScript however works in a different manner. It does not enforce user to pass values for all the parameters during function call. In such case, a variable that has not been assigned a value is of type `undefined`. 

Let's take a look at the example. `WelcomeUser` function defined below takes two parameters `userName` and `welcomeMessage`. In the first call, we are passing arguments 'Scott' and 'Good Morning' to `WelComeUser` function, and it logs it as expected. No surprises here.

Now, when we call the function without passing same number of arguments, JavaScript treats `that` argument as undefined. So in second call, when we pass 'Prasad' as an `usreName` argument, `welcomeMessage` gets initialized to undefined, so it displays the output as 'Prasad undefined!'

Similarly, when we call the function without passing any arguments, both the parameters gets initialized to `undefined` and it displays the output as 'undefined undefined!'

{% highlight javascript %}

    function WelcomeUser(userName, welcomeMessage) {
        console.log(userName + " "  + welcomeMessage + "!");
    }

    WelcomeUser("Scott", "Good Morning");
    WelcomeUser("Prasad");
    WelcomeUser();

{% endhighlight %}

Point to note that, unlike C# compiler, JavaScript compiler wont display any compile time error for undefined parameters. So now the question is how to handle undefined parameters? You can use `undefined` and the strict equality and inequality operators to determine whether a variable has a value or not. So let's modify above code to do the same.

{% highlight javascript %}

    function WelcomeUser(userName, welcomeMessage) {
        if (userName === undefined) {
            userName = "Admin";
        }
        if (welcomeMessage == undefined) {
            welcomeMessage = "Welcome";
        }
        console.log(userName + " " + welcomeMessage + "!");
    }

{% endhighlight %}

As you can easily notice, in the above code snippet, we have set `userName` to 'Admin' and `welcomeMessage` to 'Welcome', if calling code doesn't pass the argument value.

##  7. Truthy and Falsy

In C#, true and false values are always represented by a boolean variable or an boolean expression. The controlling conditional expression of an if-statement is a boolean-expression, which must return either true or false. If the condition doesn't return a boolean value, a compile time error occurs. Let's look into the code now.
 
The `displayFlag` boolean variable is initialized to true, so the statement enclosed in `if` block will always execute and outputs "Display flag is true".
 
{% highlight c# %}

    bool displayFlag = true;
    if (displayFlag)
    {
        Console.WriteLine("Display flag is true");
    }

{% endhighlight %}
 
Below code block shows simplest example of boolean expression, which must return either true or false value [True in this case].
 
{% highlight c# %}

    if (2 > 1)
    {
        Console.WriteLine("2 is greater than 1");
    }

{% endhighlight %}
 
As we discussed in the earlier sections, C# is a statically typed language, so the operands in a boolean expression should be compatiable for equality operation. For e.g. comparing a int variable with a string would result into a compile time error as shown in below example.
 
{% highlight c# %}

    var firstNumber = 10;
    var secondNumber = "10";
    if (firstNumber == secondNumber) { } // compile time error

{% endhighlight %}
 
To ensure the controlling conditional expression of an if-statement return a boolean-expression, we need to typecast the `if` statement as shown below
 
{% highlight c# %}

    var firstNumber = 10;
    var secondNumber = "10";
    var equalNumber = ((bool)(firstNumber.ToString() == secondNumber));

{% endhighlight %}
 
In JavaScript, 0, false, '', undefined, null, NaN are falsy values. All other value are truthy.
 
{% highlight javascript %}

    if (!0) { console.log("0 is falsy");}
    
    if (!false) { console.log("false is falsy"); }
    
    if (!'') { console.log("'' is falsy"); }
    
    if (undefined) { console.log('undefined is falsy'); }
    
    if (!null) { console.log('null is falsy'); }
    
    if (!0/0) { console.log('NaN is falsy'); }

{% endhighlight %}
 
Note that, a space character [' '] is a trythy value in JavaScript, and so does the Infinity.

{% highlight javascript %}

    if (1 / 0) { console.log('Infinity is truthy'); }
    
    if (' ') { console.log("' ' is truthy"); }

{% endhighlight %}

##  8. Curly braces and return statement

Both C# and JavaScript languages provides a `return` statement to return the execution control to the caller, however there is one subtle gotcha when you used it along with curly braces in JavaScript.
 
You will find endless arguments on web on 'whether the curly brace should appear on the same line as block construct [class, function etc.] or whether they should appear on their own line'. In C#, it doesn't matter really. It's a person preference to have it on the same line or new line, however I would suggest you to choose one and stick to it.
 
`Add` function defined in below code listing contains curly braces on new line, while the `GetDefaultCategory` function contains opening curly brace and return statement on different lines, which C# compiler will happily compile.
 
{% highlight c# %}

    public static int Add(int num1, int num2)
    {
        return num1 + num2;
    }
    
    public static dynamic GetDefaultCategory(){
    return
        new
        {
                Name = "General"
        };
    }

{% endhighlight %}
 
JavaScript however behaves differently in this case. The `Add` function is similar to C# version and will always display the addition of two numbers passed in. However `GetDefaultCategory` function defined below will simply return the call to the caller without returning the object literal. This is due to _automatic semicolon insertion_ feature of JavaScript. In this case, since we have opening curly brace on the next line after return statement, JavaScript compiler will insert a semicolon after return statement causing it to exit from the function without returning any value.
 
{% highlight javascript %}

    function Add(num1, num2)
    {
        return num1 + num2;
    }
    
    function GetDefaultCategory() {
        return
        {
            name: "General"
        }
    };

{% endhighlight %}
 
This can easily get more difficult as compiler doesn't return any error in this case, however caller will get a null reference rather than the object literal. To avoid such kind of situation, always use curly brace on the same line as return statement or block construct, as shown below
 
{% highlight javascript %}

    function Add(num1, num2){
        return num1 + num2;
    }
    
    function GetDefaultCategory() {
        return {
            name: "General"
        }
    };

{% endhighlight %}

##  9. this keyword 
The `this` keyword in C# is generally used for two purposes

1. Access the current instance of the class
2. To add extension methods on existing types without creating new types.

Let's discuss both of these options in detail
 
Following code listing contains an `Employee` class with two properties which has been protected by private setters, so only the parameterized constructor can initialize these values. Note that the constructor uses `this.Id` syntax to access the property on a given instance. Similarly, overridden `ToString` method retrieves class properties using `this` keyword.

{% highlight c# %}

    public sealed class Employee
    {
        public int Id { get; private set; }
        public string Name { get; private set; }

        public Employee(int id, string name)
        {
            this.Id = id;
            this.Name = name;
        }

        public override string ToString()
        {
            return string.Format("ID : {0} , Name : {1}", this.Id, this.Name);
        }
    }

{% endhighlight %}
 
C# 3 introduced _Extension Method_ concept in the language. Extension methods enable you to add methods to existing types without creating a new derived type, recompiling, or otherwise modifying the original type. This includes even adding extension method on sealed type like `string` or `Employee` class defined in earlier code listing. Below code listing adds an extension method `PrintNameAndLength` to Employee class. Note the `this` keyword used in the `PrintNameAndLength` signature, which denotes extension method on `Employee` type.
 
{% highlight c# %}

    public static class EmployeeExtension
    {
        public static string PrintNameAndLength(this Employee emp)
        {
        return emp.Name + " : " + emp.Name.Length;
        }
    }

{% endhighlight %}
 
Once declared, you can invoke it as a normal instance method as shown in below code example

{% highlight c# %}

    var emp = new Employee(1, "Prasad");
    Console.WriteLine(emp.PrintNameAndLength());

{% endhighlight %}
 
The most common extension methods are the LINQ standard query operators that add query functionality to the existing `System.Collections.IEnumerable` and System.Collections.Generic.IEnumerable<T> types.

Enter JavaScript.

The `this` keyword in JavaScript behaves totally differently as compared to C#. Additionally it works differently for different contexts. Let's understand it through examples

**Global context**
In a global execution context, `this` refers to global object. i.e. in web browsers, global object refers to `window`

{% highlight javascript %}

    console.log(this); //outputs window
    console.log(this.document == document); //returns true

{% endhighlight %}

Below code defines a `Print` function in the global context. If you pass the function as a parameter to `console.log` statement, it will return the function definition, rather than calling the function.

{% highlight javascript %}

    this.Print = function() {
        console.log("In print");
    };

    console.log(this.Print);

{% endhighlight %}

If you want to call the function, simply call it using `Print();`syntax.

Please note, defining your own functions in a global execution context is a bad practice as you can unknowingly override function declared with same name in referenced JavaScript libraries. To avoid this situation, always enclose the function inside a namespace.

**Function context**

Inside a function context, value of `this` depends on how the function was called. In a non strict mode, the value of this must be an object, so by default it gets initialized to global object. For web browser, its a `window` object.

{% highlight c# %}

    function Test() {
        return this;
    }
    console.log(Test()); //window

{% endhighlight %}

In a strict mode, if value of `this` is not defined, it gets initialized to `undefined`. However it can be set to any value before entering into execution context.

{% highlight javascript %}

    function TestStrict() {
        "use strict";
        return this;
    }
    console.log(Test()); //undefined

{% endhighlight %}

**Object method context**

C# developers are already familiar with this execution context. When a function is called as a method of an object, its `this` is set to the object the method is called on. In the code listing below, we are calling `GetNameAndAge` funtion on `cust` object, so `this` refers to the `cust` object itself. Note `cust` object exposes `Name` and `Age` properties, which are then accessible using `this`

{% highlight javascript %}

    var cust = {
        Id: 1,
        Name: "Prasad",
        Age : 35,
        GetNameAndAge: function () {
            return this.Name + ' ' + this.Age;
        }
    }
    console.log(cust.GetNameAndAge()); 

{% endhighlight %}

**Constructor context**

When a function is used as a constructor (with the `new` keyword), its this is bound to new object being constructed. In the code statement below, `this` refers to `Customer` object `newCustomer`

{% highlight javascript %}

    function Customer(name, age) {
        this.Name = name;
        this.Age = age;
        this.PrintCustomer = function() {
            console.log(this.Name);
        };
    }

    var newCustomer = new Customer('Colin', 40);
    newCustomer.PrintCustomer();

{% endhighlight %}

**call context**

Things gets slightly weird for a C# developer, when `this` gets referred inside a function, which gets called using `call` function :)

`add` function below contains only two parameters [c & d], however function definition refers to `this.a` and `this.b`, which somehow needs to be passed to the function. 

{% highlight javascript %}

    function add(c, d){
        return this.a + this.b + c + d;
    }

{% endhighlight %}

The first parameter in JavaScript `call` function is the object to be used as `this` during function execution. So in the code listing below we have constructed an object `addParamObject` and used it in `call` function as first parameter. So when `add` function gets called, `this` refers to `addParamObject` object and gets the respective values for `this.a` and `this.b`.

{% highlight javascript %}

    var addParamObject = {a:1, b:3};
    add.call(addParamObject, 5, 7); 

{% endhighlight %}

I hope by now you have noticed execution similarity between `call` function and C# extension methods.

## 10. Hoisting
As we examined in earlier sections, JavaScript supports only function level scope as compared to C# which has different scope rules [class, method, block]. However, it’s worth to find out how JavaScript handles the scoping.
 
In JavaScript, all the variable declared inside a function share same scope. Behind the scene, JavaScript hoists all the variable declaration to the top of the function and initialize them to undefined. Their assignment still remain on the same line where it was declared. This process is known as 'Variable Hoisting'.
 
Let's take a look at simple JavaScript function defined below.

 
{% highlight javascript %}

    function doSomething() {
        console.log(someVariable); //undefined
    
        var someVariable = 100;
        console.log(someVariable); // 100
    
        if (true){
            someVariable = 200;
            console.log(someVariable); // 200
        }
    }

{% endhighlight %}
 
JavaScript hoist the variable `someVariable` to the top of the function `doSomething` and initialize it to `undefined`. Below is the code definition after the hoisting is done. Since variable was gets hoisted to the top of the function, it can be accessed from anywhere inside the function. This is the main reason why JavaScript does not support block scoping.
 
{% highlight javascript %}

    function doSomething() {
        var someVariable = undefined;
        console.log(someVariable); //undefined
    
        someVariable = 100;
        console.log(someVariable); // 100
    
        if (true){
            someVariable = 200;
            console.log(someVariable); // 200
        }
    }

{% endhighlight %}
 
And then JavaScript does function hoisting as well! However, the hoisting rules for variable and functions are different. JavaScript provides two ways to define a function in a program - **Function Expression** and **Function Statement**. The easiest way to tell them apart is, if it starts with the function keyword, then it is a function statement.
 
{% highlight javascript %}

    try {
        printOne();
        printTwo();
    
    } catch (e) {
        console.log(e.message);
    }
    
    // Function Expression
    var printOne = function () {
        console.log("One");
    }
    
    // Function Statement
    function printTwo() {
        console.log("Two");
    }

{% endhighlight %}
 
During function hoisting process, function statement is transformed behind the scenes into a function expression and then both the variable and the assignment are hoisted to the top of the function scope. So the function gets converted as shown below
 
{% highlight javascript %}

    var printOne = undefined,
        printTwo = undefined;
    
    printTwo = function(){
        console.log("Two");
    }
    
    try {
        printOne();
        printTwo();
    } catch (e) {
        console.log(e.message);
    }
    
    printOne = function () {
        console.log("One");
    }

{% endhighlight %}
 
Note that, function expression follows same rule as variable hoisting, but function statement behaves differently.

## Summary
Ah!, we did it! I hope this post helped you to understand basic differences between JavaScript and C# languages, and common traps associated with it. 

Please provide your feedback in the comments section below.

Namaste.