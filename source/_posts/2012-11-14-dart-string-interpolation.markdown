---
layout: post
title: "Dart String Interpolation"
date: 2012-11-14 11:23
comments: true
categories: 
---

You can access the value of an expression inside a string by using `${expression}`.
    var greeting = "Hello";
    var person = "Rohan";
  
    print("${greeting}, ${person}!"); // prints "Hello, Rohan!"
  
If the expression is an identifier, the `{}` can be skipped.
    print("$greeting, $person");
  
If the variable inside the `{}` isn't a string, the variable's `toString()` method is called:
    int x = 5;
    print("There are ${x.toString()} people in this room");

The call to `toString()` is unnecessary (although harmless) in this case: `toString()` is already defined 
for ints and Dart automatically calls `toString()`. What this does mean, though, is that it is the user's
responsibility to define a `toString()` method when interpolating user-defined objects.
  
You can interpolate expressions of arbitrary complexity by placing them inside `${}`:

A ternary `if..else`:
    int x = 5;
    print("There are ${x < 10 ? "a few" : "many"} people in this room");
  
List and Map operations:
    List list = [1, 2, 3, 4, 5];
    print("The list is $list, and when squared it is ${list.map((i) {return i * i;})}");
    // The list is [1, 2, 3, 4, 5], and when squared it is [1, 4, 9, 16, 25]
 
    Map map = {"ca": "California", "pa": "Pennsylvania"};
    print("I live in sunny ${map['ca']}");
    // I live in sunny California

Notice above that you can access `$list`(an identifier) without using `{}`, but the call to `list.map`(an expression) 
needs to be inside `{}`. Similarly, in the example below, `$x` works, but `{}` are required for `-x`:

    print("x = $x and -x = ${-x}");
    // x = 5 and -x = -5

Expressions inside `${}` can be arbitrarily complex:

    List names = ['John', 'Sophena', 'Henrietta'];
    print("${
      ((names) {
          return names[(new math.Random()).nextInt(names.length)];
      })(names)} is the most valueable member of our team");

The code above defines an anonymous function to pick a random name from a list and then calls that function with 
`names` as an argument. All of this is done as part of string interpolation.

Creating a function and immediately calling it is useful in a lot of situations (it is a common practice in Javascript); doing so as part
of string interpolation is needlessly clever and should probably be avoided.
