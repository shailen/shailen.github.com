---
layout: post
title: "why use Dart?"
date: 2012-11-12 14:19
comments: true
categories: 
---

Building web applications is hard. Building _**large**_ web applications is a real challenge. Given this, I am super
excited by the emergence of Dart as a structured web programming language for building applications for the modern web.

I am mostly a Ruby and Python hacker. I have spent the last few years doing web programming and this means that I
have spent a lot of time writing Javascript. I **_really_** like coding in Javascript: I like its (mostly) coherent OO/functional hybrid syntax 
, I find it expressive and I like to make use of its
rich and rapidly evolving ecosystem of frameworks and libraries.

Javascript started off as small language for animating mostly static web pages. As the
web has evolved, Javascript code-bases have become larger (my last project, a Rails 3 app using Backbone, had more Javscript than 
Ruby code in it). Large codebases require careful use of code reuse and organization; through the use of 'classes' and inheritance,
the module and sandbox design patterns, popular MVC frameworks like Backbone and Angular, Javascript developers have sought to bring
more structure to their code.

I very much view Dart as a logical next step in this quest for greater code organization. The Dart project is a serious - and long awaited - 
attempt at creating an alternative to writing web applications using Javascript; it is likely to appeal to those who want to 
retain the relative ease of current web development, but want a language with more structure than what is available to them today.
Building large web applications has been quite daunting - even heroic - and Dart aims to make the development process easier.
That Dart is a "batteries included" project (the language comes with libraries, a packet manager, an editor, and a VM) and Dart code executes fast, make it an
even more attractive option for modern web development.

Here are a few salient points about Dart:

- Dart is open source
- it has a syntax that is likely to be familiar to most programmers
- it is relatively easy to learn
- it compiles to javascript that can be run in any modern browser
- it can also be executed directly using a Chromium-based browser (nicknamed [Dartium](http://www.dartlang.org/dartium/)) that includes the Dart virtual machine (VM)
- it can coexist with javascript through the use of the [interop library](http://www.dartlang.org/articles/js-dart-interop/)
- it runs in the client and the server
- it comes with a lightweight [editor](http://www.dartlang.org/docs/editor/) that you can use to write and debug applications
- Dart programs run _fast_ (many of the people who created Chrome's V8 are leading the Dart project)
- it offers concurrency through the use of [isolates](http://www.dartlang.org/docs/dart-up-and-running/ch03.html#ch03-dartisolate---concurrency-with-isolates)
- Dart has an **optional** type system that can help you organize your code, lead to better debug messages in the editor and act as documentation
- it comes with batteries included: an [HTML library](http://www.dartlang.org/docs/dart-up-and-running/ch03.html#ch03-dart-html-using-html5-apis)
for DOM manipulation, an [IO library](http://www.dartlang.org/docs/dart-up-and-running/ch03.html#ch03-dartio---file-and-socket-io-for-command-line-apps)
and a [unittest library](http://api.dartlang.org/docs/bleeding_edge/unittest.html)
- it encourages modular code organization through the use of libraries
- it has a packet manager ([pub](http://pub.dartlang.org/)) so you can integrate code from across the Dart ecosystem

Is Dart going to change how we write web applications? While its still early - the language just had its M1 (Milestone 1)
release and is still not at the 1.0 stage - what I see so far is super encouraging. 
