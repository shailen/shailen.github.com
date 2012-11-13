---
layout: post
title: "why use Dart?"
date: 2012-11-12 14:19
comments: true
categories: 
---

I am mostly a Ruby and Python hacker. I have spent the last few years building web applications and this means that I
have spent a lot of time writing Javascript. 

I **_really_** like Javascript: it has a (mostly) coherent OO/functional hybrid syntax 
(derived from Self, Scheme and C), is highly expressive and
has a rich and rapidly evolving ecosystem of frameworks and libraries.

But Javascript is undeniably quirky (note that not every language has a must-read "[Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742/ref=sr_1_1?s=books&ie=UTF8&qid=1352778037&sr=1-1&keywords=crockford)" book ;)), wasn't designed with
the modern web in mind, and despite the proliforation of code organizing practices (using 'classes', encapsulating code using the
module and sandbox patterns, using MVC frameworks like Backbone and Angular, etc.), building large-scale applications with it remains hard, almost heroic.
This need not be the case.

The Dart project is a serious attempt at creating an alternative to writing web 
applications using Javascript. Dart is likely to appeal to those who want to retain the relative ease of current web development,
but want a language with more structure than what is available to them today.

Here are a few salient points about Dart:

- Dart is open source
- it has a syntax that is likely to be familiar to most programmers
- it is relatively easy to learn
- it compiles to javascript that can be run in any modern browser
- it can also be executed directly using a Chromium-based browser (nicknamed [Dartium](http://www.dartlang.org/dartium/)) that includes the Dart virtual machine (VM)
- it can coexist with javascript through the use of the [interop library](http://www.dartlang.org/articles/js-dart-interop/)
- it runs in the client and the server
- it comes with a lightweight [editor](http://www.dartlang.org/docs/editor/) that you can use to write and debug applications
- Dart programs run fast (many of the people who created Chrome's V8 are leading the Dart project)
- it offers concurrency through the use of [isolates](http://www.dartlang.org/docs/dart-up-and-running/ch03.html#ch03-dartisolate---concurrency-with-isolates)
- Dart has an **optional** type system that can help you organize your code, lead to better debug messages in the editor and act as documentation
- it comes with batteries included: an [HTML library](http://www.dartlang.org/docs/dart-up-and-running/ch03.html#ch03-dart-html-using-html5-apis)
for DOM manipulation, an [IO library](http://www.dartlang.org/docs/dart-up-and-running/ch03.html#ch03-dartio---file-and-socket-io-for-command-line-apps)
and a [unittest library](http://api.dartlang.org/docs/bleeding_edge/unittest.html)
- it encourages modular code organization through the use of libraries
- it has a packet manager ([pub](http://pub.dartlang.org/)) so you can integrate code from across the Dart ecosystem

Is Dart going to change how we write web applications? I don't know, it is too early to tell. The language just had its M1 (Milestone 1)
release and is still not at the 1.0 stage. But what I see so far is quite encouraging.
