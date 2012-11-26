---
layout: post
title: "using boolean expressions for assignment in Dart"
date: 2012-11-17 21:52
comments: true
categories: [dart, dartlang, booleans, assignment] 
---

I often use boolean evaluation in Javascript for assignment:

    var Point = function(options) {
      options = options || {};
      this.x = options['x'] || 0;
      this.y = options['y'] || 0;
    }

And in Ruby, it is idiomatic to do something like this:
    age ||= 16; // if age is `nil` or `false`, assign it a value of 16

or, this:
    a, b, c = 1, 2, 3
    d = a && b && c
    // d gets the value of c, 3

In each case, the boolean expressions return the value of the last evaluation. Can I do something similar in Dart?
Is this code legal?

    var x; // x is null
    var y = x || 10;

In checked mode, no; in unchecked mode, this works, but the result may surprise you.

Based on other languages I have used, I would expect `y` to equal `10`.
In Dart, this does not happen and `y` gets assigned a value of `false`. Why? Since `10` is not explicitly `true`, it evaluates to 
`false`. All things that are not explicitly `true` in Dart, are false (see my [post](http://shailen.github.com/blog/2012/11/16/booleans-in-dart/)
from yesterday if this isn't clear).

Now, in checked mode (you are using checked mode, right?), `y` doesn't get a value at all because we get an error:

    Unhandled exception:
    type 'Null' is not a subtype of type 'bool' of 'boolean expression'.

This is Dart's way of telling us that it is not going to do implicit boolean conversion for us when using `||` and it expects to see a boolean where it
now sees a `null` (`x` is null).  Changing the code to:

    var y = 5 || 10;

we get a slightly different error message:

    Unhandled exception:
    type 'int' is not a subtype of type 'bool' of 'boolean expression'.

No good. Dart wants booleans around the `||`. This works fine, but it isn't what we are looking for:
    int x = 10;
    bool y = x % 3 == 1 || x % 5 == 2;
    // y is true

So, is there a correct way to handle assignment based on boolean evaluation? Yes. Don't rely on `||` or `&&` to implicitly
handle this for you; instead, explicitly check for truthyness yourself:
    int y = x == null ? 0 : x;

If `x` is null, `y` will be `0`; else, it will have the value of `x`. Quite clear and readable.

Boolean based assignment is common in JavaScript because there we don't have as rich an understanding 
of keyword arguments and default values as Dart has. In Dart, we can do this:

    myFunction({x: 0, y: 0}) {
      ...
    }

If you need to check whether a parameter was passed a value or not, you can easily do so:

    myFunction([x=0]) {
      if (?x) {
        ...
      }
    }
    
The lessons of all this? **Use checked mode**. Don't rely on Dart's boolean expressions to magically return boolean values; if such values
are needed, obtain them directly yourself. Be explicit. Use default arguemnts in functions and methods. Use `?` to check if a parameter was passed.






  
