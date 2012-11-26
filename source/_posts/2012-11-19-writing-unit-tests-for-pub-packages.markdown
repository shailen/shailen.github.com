---
layout: post
title: "writing unit tests for pub packages"
date: 2012-11-19 11:18
comments: true
categories: [dart, dartlang, unittest, pub, dart-editor]
---

In this post I'm going to show you how to create a really simple Dart package. Because we would never want to
write a package without testing the code in it, I will also introduce you to Dart's quite excellent `unittest` framework.

[Pub](http://pub.dartlang.org/) is Dart's package mananger. After working through this post, you will be able to bundle your Dart libraries
and share them with others on Pub.

For the purposes of this post, our package will be very basic and consist of a singe `range()` function modeled roughly on the Python
builtin function with the same name.  

`range()` will have the following signature: 

    List<num> range(num start, num stop, [num step = 1]);

It will return a list of ints between `start` and `stop` separated by `step` steps. Here is some sample usage:

    range(0, 4);    // [0, 1, 2, 3]
    range(1, 6, 2); // [1, 3, 5]

Let's get started.

## Create a simple package

Open up Dart Editor and create a `New application` called `range`. For this example, we will not be creating a web project, so uncheck `Generate 
content for a basic web app` when you create the application. But do make sure that `Add Pub support` is checked.

Delete the automatically created `bin` directory. We won't be needing it.

Create a top level `lib` directory. Inside `lib`, create a `range.dart` file: the code for `range()` will go in here. 

There are other directories and files that you should create - a README, a LICENSE, a `doc/` folder for documentation,
an `example/` folder with examples showing usage of your package, etc. - but our focus here is on how to write unittests, so we'll skip over those
files and directories for now. To know what else you should be doing to make this a _respectable_ package, see this excellent writeup
on [package layout conventions](http://pub.dartlang.org/doc/package-layout.html) on the [pub site](http://pub.dartlang.org/doc/package-layout.html).

## Add unittest package to your library

With the basic files created for our package, let's open up `pubspec.yaml` and add some metadata for our pub
package (read more about [Pubspec format](http://pub.dartlang.org/doc/pubspec.html)). Every package **must** contain a `pubspec.yaml`; in fact, it 
is this file that makes it a package. 

Add a simple description for the package and specify its only dependency, the unittest package. Your `pubspec.yaml` should look like this:

    name:  range
    description:  An approximate implementation of the range() function in Python.
    
    dependencies:
      unittest: { sdk: unittest }

Run `pub install` (you can find it under `Tools` in the Editor). This will create a `pubspec.lock` file and a bunch of
symlinks that are all necessary for the plumbing to work correctly; fortunately, Dart handles all these details for us.

## Write some code

Let's create a bare-bones implementation for `range()`.  Add the following code to `lib/range.dart`:
    
    library range; 

    List<int> range(int start, int stop, [int step=1]) {
      if (start >= stop) {
        throw new ArgumentError("start must be less than stop");
      }

      List<int> list = [];
      
      for (var i = start; i < stop; i+= step) {
        list.add(i);
      }

      return list;
    }
    
## Write some tests

Create a top level `test` directory. Inside `test`, create a `range_test.dart` file: your tests will go in here.
But before we can write our tests, `test/range_test.dart` needs access to the `unittest` package and the `range` library. At the top of
`range_test.dart`, add these `import` statements:

    import 'package:unittest/unittest.dart';
    import 'package:range/range.dart';


Now add a couple of tests (we'll need many more to really test `range()`, but these will do for now). Your `range_test.dart` should look like this:

    import 'package:unittest/unittest.dart';
    import 'package:range/range.dart';

    void main() {
      test("range() produces a list that starts at `start`", () {
        expect(range(0, 4)[0], equals(0));
      });

      test("range() throws an exception when start > stop", () {
        expect(() => range(5, 2), throwsA(new isInstanceOf<ArgumentError>()));
      });      
    }

An individual test goes inside `test()`. `expect()` evaluates the equality between the expected and actual values. A string
argument to `test()` describes the purpose of the test. 

Run the tests (press the green arrow in the Editor; or, press `CMD-R` if you are using a Mac; or, type `dart test/range_test.dart` on the command line).
Watch the output produced by the editor:

    unittest-suite-wait-for-done
    PASS: range() produces a list that starts at `start`
    PASS: range() throws an exception when start > stop
    
    All 2 tests passed.
    unittest-suite-success


## Summary

So, we managed to create a minimal Dart package, made it (barely) good enough to put on `pub`, and wrote a few tests.  This is a good start, of course, 
but we have only scratched the surface of how we should test our packages. For a very thorough explanation of how the `unittest` framework works
in Dart, I would highly recommend a careful reading of [Unit Testing with Dart](http://www.dartlang.org/articles/dart-unit-tests/) by Google engineer
Graham Wheeler. He goes into considerable details about writing tests, defining `setUp()` and `tearDown()`, running asynchronous tests, using 
and creating matchers, and configuring your test environment.
