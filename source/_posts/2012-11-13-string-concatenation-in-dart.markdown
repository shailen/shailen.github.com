---
layout: post
title: "string concatenation in dart"
date: 2012-11-13 17:33
comments: true
categories: [Dart, strings, StringBuffer]
---

Let us start with a simple helloworld.dart example and use it to see how string concatenation works in Dart.

    void main() {
      print("hello, world!");
    }

The above works, obviously. Now, if we were given this greeting as two strings, "hello, " and "world!", and asked
to join them together, we might be tempted to do:

    "hello, " + "world!"
     
This works in lots of languages, but not in Dart. The + operator has not been overloaded in the String class, the above code throws
a `NoSuchMethodError`.


Not a problem: Dart gives us lots of ways to contcatenate strings. I list the most common ways
below. _Above the examples you will see some **crude** benchmarks that I calculated by running each example a million
times on my MacBook. These can give a **general** sense of the relative efficiency of each method_.

The easiest, most efficient way to concat strings is by using adjacent string literals:

    .041 seconds
    String a = "hello, " "world!";

This still works if the adjacent strings are on different lines:

    .040 seconds
    String b = "hello, "
        'world!';

Dart also has a `StringBuffer` class, and this can be used to build up a `StringBuffer` object
and convert it to a string by calling `toString()` on it:

    .689 seconds
    var sb = new StringBuffer();
    ["hello, ", "world!"].forEach((item) {
      sb.add(item);
      });
    String c = sb.toString();
    
The `Strings` class (notice the plural) gives us 2 methods, `join()` and `concatAll()` that 
can also be used. `Strings.join()` takes a delimiter as a second argument:

    .408 seconds
    String d = Strings.join(["hello", "world!"], ", ");

    .385 seconds
    String e = Strings.concatAll(["hello", "world"]);

All of the above work, but if you are looking for a `+` substitute, use adjacent string literals; 
if you need to join a list of strings using a delimiter,
use `Strings.join()`. If you plan on building a very long string, the 
`StringBuffer` class can gather the components quite efficiently and convert them to a string only 
when needed. 

You can also use string interpolation; that will be the subject of my next post.

