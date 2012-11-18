---
layout: post
title: "using boolean expressions for assignment in Dart"
date: 2012-11-17 21:52
comments: true
published: false
categories: [Dart, booleans, assignment] 
---

I often do this in Javascript:

    var Point = function(options) {
      options = options || {};
      this.x = options['x'] || 0;
      this.y = options['y'] || 0;
    }

in Ruby, I do this:
    age ||= 16; // if age is `nil` or `false`, assign it a value of 16

or, this:
    a, b, c = 1, 2, 3
    d = a && b && c
    // d will equal 3

In these cases, the boolean expressions return the value of the last evaluation. So, how should this be done
in Dart? Consider this code:

    var x;
    var y = x || 10;

In checked mode, this generates an exception (more on that in a moment). What do you think happens in unchecked mode?  What
is the value of `y`?  

At first glance, the code looks reasonable, but the answer is _really_ problematic. Based on other languages I have used, I would expect `y` to equal `10`.
In Dart, this does not happen. `y` actually gets assigned a value of `false`. Since 10 is not explicitly `true`, it evaluates to 
`false`. This is exactly what Dart is supposed to do. See my [post](http://shailen.github.com/blog/2012/11/16/booleans-in-dart/) from yesterday it this isn't clear.

Now, in checked mode (you are using checked mode, right?), `y` doesn't get a value at all because we get an error:

    Unhandled exception:
    type 'Null' is not a subtype of type 'bool' of 'boolean expression'.

This is Dart's way of telling us that it is not going to do implicit boolean conversion for us when using `||` and it expects to see a boolean where it
now sees a `null` (`x` is null).  Changing the code to:

    var y = 5 || 10;

we get a slightly different error message:

    Unhandled exception:
    type 'int' is not a subtype of type 'bool' of 'boolean expression'.

No good. Dart wants booleans around the `||`. Something like this works fine:
   int x = 10;
   bool y = x % 3 == 1 || x % 5 == 2;
   // y is true

So, how do we correctly handle assignment based on boolean evaluation? We do an explicit test and not rely on `||` or `&&` to implicitly
handle this for:
    int x; // x is null
    int y = x == null ? 0 : x;
    // y now has a value of 0

The lessons of all this? **Use checked mode**. Don't rely on Dart's boolean expressions to magically return boolean values; if such values
are needed, obtain them directly yourself. Its much clearer to be explicit.






  
