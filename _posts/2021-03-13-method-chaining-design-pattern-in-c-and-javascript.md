---
title: Method Chaining Design Pattern in C# and JavaScript
categories:
  - design pattern
tags:
  - c#
  - javascript
# toc: true
# toc_label: "Traps"
# toc_icon: "cog"
---

One of the most commonly used and useful design pattern in programming language is Method Chaining. As the name suggests, it allows you to chain / call multiple functions on the same object consecutively, without any need to specify object name during each method call. This pattern is heavily used in JavaScript libraries like jQuery, AngularJS and in Language Integrated Query [LINQ] feature of C#. 

In the first part of this article, we will cover how this pattern is implemented in jQuery and C# LINQ. The code snippet presented in the article will help you to understand how this pattern improves code readability. In the second part, we will dive into more detail and implement the pattern from scratch using C# and JavaScript. 

## jQuery - Without Method Chaining

In below code snippet, first we are identifying DOM element `outputDiv` using jQuery Id selector syntax. Next, we are setting up values for background, height and width properties of the identified DOM element. Last code line animates the DOM element using the `fadeIn` method for 200 milliseconds. Take a note that, we have to repeat DOM element name `$div` each time while setting up the property or calling `fadeIn` method.

{% highlight javascript %}

    var $div = $('#outputDiv');
    $div.css('background', 'blue');  
    $div.height(100);
    $div.width(100);
    $div.fadeIn(200);

{% endhighlight %}

## jQuery - With Method Chaining

Let's modify the code snippet discussed above and implement it using method chaining pattern. Since most of the functions in jQuery implemented to support method chaining, it makes it really easy to refactor five lines of code described above into a single line.

{% highlight javascript %}

    $('#outputDiv').css('background', 'blue').height(100).width(100).fadeIn(200);

{% endhighlight %}

However, it's a common practice in jQuery to provide method name on a separate line as shown in below code snippet to provide more code readability. Note that, we dont have to assign the value of `outputDiv` DOM element to any variable and repeat its name for each property setter or during method call.

{% highlight javascript %}

    $('#outputDiv')
    .css('background', 'blue')
    .height(100)
    .width(100)
    .fadeIn(200);

{% endhighlight %}

Even in this trivial example, we managed to reduce number of code lines and improved readability, which is really good.

## C# - LINQ

[LINQ](http://msdn.microsoft.com/en-us/library/bb397933.aspx "C# LINQ") has simplified C# development to a great extent. If you're not familiar with it, stop reading, find useful articles about it on Web and then come back. It's that cool!

In this article we will just focus on method chaining pattern implementation in C#. Code snippet shown below gets the list of running processes on user machine using `Process.GetProcesses()` code statement. The `Where` clause / function filters the retrieved processes [from step 1] and returns only the processes which has more than 5 threads. The `OrderBy` function sorts the processes by their name and `Select` function returns the processes name. 

Again, point to note that, we are not using any variable to store the running processes and then using it to filter and sort the data, rather we are using method chaining pattern to simply call the next method in the chain on previous method's return value.

{% highlight c# %}

    var query = Process.GetProcesses()
                    .Where(p => p.Threads.Count > 5)
                    .OrderBy(p => p.ProcessName)
                    .Select(p => p.ProcessName);

    foreach (var process in query)
    {
        Console.WriteLine("{0}", process);
    }

{% endhighlight %}

Okay, now we have fair understanding of the pattern, lets implement it using C# and JavaScript.

## Method Chaining Pattern Implementation using C#

The `Person` class defined below contains three properties viz `Name`, `Age` and `City`. Though its not absolutely necessary in this example to specify `private` access specifier on these properties, I have set it so that the property value can be set using declared methods `SetName`, `SetAge` and `SetCity`. The most important part of these functions, after setting the property value, they are returning `Person` object using `this`, which allows us to chain the methods.

{% highlight c# %}

    class Person
    {
        public string Name { get; private set; }
        public int Age { get; private set; }
        public string City { get; private set; }

        public Person SetName(string name)
        {
            this.Name = name;
            return this;
        }

        public Person SetAge(int age)
        {
            this.Age = age;
            return this;
        }

        public Person SetCity(string city)
        {
            this.City = city;
            return this;
        }

        public void Save()
        {
            Console.WriteLine("Saving Person [Name: {0}, Age: {1}, City: {2}]...", 
                            this.Name, this.Age, this.City);
        }
    }

{% endhighlight %}

Once you declare class in this way, you can instantiate it and use method chaining as shown in below code example

{% highlight c# %}

    new Person().SetName("Prasad Honrao")
                .SetAge(35)
                .SetCity("Pune")
                .Save();


{% endhighlight %}

Yes, it's that easy!

## Method Chaining Pattern Implementation using JavaScript

JavaScript implements method chaining pattern is the same way as C# or vice versa :).

{% highlight javascript %}

    var Person = function() {
        this.name = "";
        this.age = 0;
        this.city = "";

        this.setName = function(name){
            this.name = name;
            return this;
        };

        this.setAge = function(age){
            this.age = age;
            return this;
        };

        this.setCity = function(city) {
            this.city = city;
            return this;
        };

        this.save = function() {
        console.log("Saving Person ["
                    + "Name : " + this.name + ", "
                    + "Age : " + this.age + ", "
                    + "City : " + this.city + "]...");
        return this;
        };
    };

{% endhighlight %}

{% highlight javascript %}

    new Person().setName("Colin")
                .setAge(35)
                .setCity("London")
                .save();

{% endhighlight %}

## Summary

In this article we covered how method chaining pattern is implemented jQuery and C# LINQ. We have also seen how similar and easy it is to implement the pattern in C# and JavaScript. I hope this article was useful to you. Let me know if you have any questions in the article's comments section.

Namaste.

## Additional References

[Wikipedia - Method Chaining](http://en.wikipedia.org/wiki/Method_chaining "Method Chaining Pattern")