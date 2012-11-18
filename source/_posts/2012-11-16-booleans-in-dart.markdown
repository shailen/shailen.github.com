---
layout: post
title: "booleans in dart"
date: 2012-11-16 18:32
comments: true
categories: 
---

We've all written code that looks something like this:
    if (x) {
      // do something
    } else {
      // do something else
    }

In Javascript, the `if (x)` would evaluate to false if `x` was `false`, `null`, `undefined`, the number `0` (or `0.0`), 
an `''` or `NaN` (these are all falsy in Javascript); otherwise it would evaluate to true.  

If this code were rewritten in Python, the `if (x)` would evaluate to false if `x` was `None`, `false`, zero (`0`, 
`0.0`, `0L`, etc.), an empty string (`''`), list (`[]`), tuple (`()`) or dict(`{}`); 
otherwise it would evaluate to true.
  
So, what are the truthy and falsy lists for Dart? To answer that, let's look at what the
language spec says about booleans:
  
    Boolean conversion maps any object o into a boolean. Boolean conversion is defined  by the function
      (bool v){
        assert(v != null);
        return identical(v, true);
      }(o)
  
In other words, anything that is not explicitly `true`, is `false` in Dart. So, in our example, if `if (x)` would evaluate to
`true` if and only if `x` equalled the the boolean literal `true` (or an expression that evaluated to `true` (`3 % 2 == 1`, say)),;
otherwise it would evaluate to false.

This is very simple. There are no truthy/falsy lists to memorize; it is all very clear, quite correct and ....

likely to get you quickly into trouble. Wait, what? 

This is legal Dart code, right? Well, that depends if you are in checked mode or not.  In unchecked mode, the code works exactly
as described above. However, in _checked mode_, any `if (x)` type constructs will generate an exception unless x is a boolean. Let's 
look at that function from the language spec again:
  
    (bool v) {
    
    }

in checked mode, v **must be a bool**. If it isn't, the Dart editor will complain and throw an exception. We will never
get to the stage of figuring out whether our `if (x)` evaluates to true or false. 

Going back to other languages for a moment: I always felt that Javascript and Python had too many falsy values. I liked
that in Ruby, only `false` and `nil` evaluated to false; everything else was true. So, instead of writing `if myList ...`, you
would write `if myList.empty? ..` in Ruby, making your intent quite clear. 
Dart actually goes beyond
Ruby in this regard and _really_ simplifies things:  `false` (and boolean expressions that return `false`) are false; 
`true` (and boolean expressions that return `true`) are true. _**Everything else is neither false nor true**_.

So, as a programmer, how should you handle the reality that the Dart's boolean symantics change based on whether you 
are in checked or unchecked mode?  My recommendation: always code in checked mode and be quite explicit about boolean 
expressions.

Avoid code like this (you will get an exception in checked mode):
  
    String s = '';
    if (s) { 
    }
  
This can be rewritten more clearly:
  
    if (s.isEmpty) {
      // notice that it is `isEmpty`, not `isEmpty()`
    }

The following:
    List myList = [1, 2, 3];
    if(myList.indexOf(1)) {};
  
should become:
    if (myList.indexOf(1) == 0) {
    }
   
and:
    int num;
    if (num) {
    }

should become:
    if (num == null) {
    }

The real take home lesson is a) listen to the static warnings b) only use booleans for boolean operations (Do not rely on implicit boolean 
conversions) c) checked mode is there to stop you immediately if you do bad things during development. Dart works pretty hard 
to force you to do boolean stuff explicitly and there are good reasons for this. Follow the warnings from the editor and your
code will be clearer, more maintainable and you will avoid a huge class of bugs.
