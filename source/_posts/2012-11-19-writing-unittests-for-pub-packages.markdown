---
layout: post
title: "writing unittests for pub packages"
date: 2012-11-19 11:18
comments: true
categories: 
---

In this post I'm going to show you how to create a really simple Dart package (something that would be suitable for [pub](http://pub.dartlang.org/),
the package manager for Dart. Because we would never want to write a package without testing the code in it, I will also introduce you to 
Dart's quite excellent `unittest` framework.

For the purposes of this post, our package will be really very basic and consist of a singe `range()` function modeled roughly on the Python
builtin function with the same name.  `range()` will take 3 arguments - `start`, `stop` and an optional `step` - and return a list of ints
between `start` and `stop` separated by `step` steps. `range(0, 4)` will return `[0, 1, 2, 3]` (using
a default `step` of `1`), `range(2, 10, 3)` will return `[2, 5, 8]`, etc.

Let's get started.

## Creating a Simple Package

Open up Dart Editor and create a `New application` called `range`. For this example, we will not be creating a web project, so uncheck "Generate 
content for a basic web app" when you create the application.

Delete the automatically created `bin` directory. We won't be needing it.

Create a top level `lib` directory. Inside `lib`, touch a `range.dart` file: the code for `range()` will go in here. 

Create a top level `test` directory. Inside `test`, touch a `range_test.dart` file: your tests will go in here.

There are other directories and files that you should create - `doc/`, `example/`, README.md, etc. - but 
our focus here is on how to write unittests, so I'll concentrate on the contents of `lib/range.dart` and `test/range_test.dart`.
To know what else you should be doing to make this a _respectable_ package, see [this excellent writeup on Package layout
conventions](http://pub.dartlang.org/doc/package-layout.html) on the [pub site](http://pub.dartlang.org/doc/package-layout.html).

## pubspec.yaml

With the basic files created for our package, let's open up `pubspec.yaml` and add a description for the package. I
added: "An approximate implementation of the range() function in Python."

Now, let's specify the necessary dependencies for the package in `pubspec.yaml`. For our simple package, we only need to add (or uncomment) a
dependency for the unittest package.
    dependencies:
      unittest: { sdk: unittest }

Run `pub install` (you can find it under `Tools` in the Editor). This will create a bunch of stuff: a `pubspec.lock` file, a `/packages` directory, and a `/test/packages` directory.

Notice the symlinks created by Dart in the packages directory for `/<path-to>/dart-sdk/pkg/unittest/lib` and `/<path-to>/range/lib`. 
`/test` also creates a symlink to `/range/packages`. These are all necessary for the plumbing to work correctly; fortunately, Dart handles all the details for us.

## Writing some Code

Now we can start writing some tests and some code. If this were a real project, I would write the tests first and use Test Driven Development (TDD) to drive the writing of the code.
But this is a demo project, so we'll just create a bare-bones implementation for `range()` and then start writing tests.

Add the following code to `lib/range.dart`:
    
    library range; 

    List<int> range(int start, int stop, [int step]) {
      if (start >= stop) {
        throw new ArgumentError("start must be less than stop");
      }

      List<int> list = [];
      f (!?step) step = 1;
      
      for (int i = start; i < stop; i+= step) {
        list.add(i);
      }
      return list;
    }
    
## Imports

With that out of the way, we can start writing tests. But first, `test/range_test.dart` needs access to the `unittest` package. At the top of
that file, add this `import` statement:

    import 'package:unittest/unittest.dart';


We also need to import the range() function. Remember the `library` declaration at the top of `range.dart`? That enables us import `range()` into our test
file. 

Add a second `import`statement to `range_test.dart`.

    import 'package:range/range.dart';

## Our first test

Now that the plubming is working, we can write (and run) our first test. Modify `range_test.dart` so that it looks like this:

import 'package:unittest/unittest.dart';
import 'package:range/range.dart';

    void main() {
      test("range() produces a list that starts at `start`", () {
        expect(range(0, 4)[0], equals(0));
      });
    }

An individual test goes inside `test()`. `expect()` evaluates the equality between the expected and actual values. A string
argument to `test()` describes the purpose of the test. 

Run the test (press the green arrow in the Editor; or, press `CMD-R` if you are using a Mac; or, type `dart test/range_test.dart` on the command line).
Watch the output produced by the editor:

    unittest-suite-wait-for-done
    PASS: range() produces a list that starts at `start`
    
    All 1 tests passed.
    unittest-suite-success

Our test passes.

## More tests

Let's add a few more tests. We should test that the range does not include `stop` and that an ArgumentError is raised 
when start >= stop. Add the tests so that your `range_test.dart` file looks likes this:

    import 'package:unittest/unittest.dart';
    import 'package:range/range.dart';

    void main() {
      test("range() produces a list that starts at `start`", () {
        expect(range(0, 4)[0], equals(0));
      });
      
      test ("range() produces a list that stops before `stop`", () {
        expect(range(0, 4).last, equals(3));
      });
      
      test("range() throws an exception when start > stop", () {
        expect(() => range(5, 2), throwsA(new isInstanceOf<ArgumentError>()));
      });
      
      test("range() throws an exception when start == stop", () {
        expect(() => range(5, 5), throwsA(new isInstanceOf<ArgumentError>()));
      });
    }

Run the tests again. They should all pass.


## Nested groups

What we have here works fine, but we can improve things a bit. Notice the repetition in the string arguments we pass to
each `test()` ("range() produces ...", "range() throws ...", etc.)? We should clean that up. Also, we have 4 tests that fall into
2 natural groups: the first two
deal with valid arguments and the remaining with invalid arguments. We should arrange our tests to more clearly reflect this grouping. Finally, there are no
tests for the optional `step` argument. We should definitely add those. Rewriting our tests, we get: 

    void main() {
      group("range()", () {
        group("produces a list", () {
          test("that starts at `start`", () {
            expect(range(0, 4)[0], equals(0));
          });
          
          test ("that stops before `stop`", () {
            expect(range(0, 4).last, equals(3));
          });
        
          test("that has consecutive values if no `step` is given", () {
            expect(range(1, 6), equals([1, 2, 3, 4, 5]));
          });
      
          test("has non-consecutive values with `step` > 1", () {
            expect(range(1, 6, 2), equals([1, 3, 5]));
          });
        });
    
        group("throws an exception", () {
          test("when start > stop", () {
            expect(() => range(5, 2), throwsA(new isInstanceOf<ArgumentError>()));
          });
    
          test("when start == stop", () {
            expect(() => range(5, 5), throwsA(new isInstanceOf<ArgumentError>()));
          });
        });
      });
    }

Much better. We use nested `group()`s to organize our tests; we pass descriptive string args to `group()` to make our intent clear; we get rid
of a lot of repetitive. Let's run our tests again:

    unittest-suite-wait-for-done
    PASS: range() produces a list that starts at `start`
    PASS: range() produces a list that stops before `stop`
    PASS: range() produces a list that has consecutive values if no `step` is given
    PASS: range() produces a list has non-consecutive values with `step` > 1
    PASS: range() throws an exception when start > stop
    PASS: range() throws an exception when start == stop
    
    All 6 tests passed.
    unittest-suite-success

## Summary

So, we managed to create a minimal Dart package, made it (barely) good enough to put on `pub`, and wrote a few tests.  This is a good start, of course, 
but we have barely scratched the surface of how we should test our packages. For a very thorough explanation of how the `unittest` framework works
in Dart, I would highly recommend a careful reading of [Unit Testing with Dart](http://www.dartlang.org/articles/dart-unit-tests/) by Google engineer
Graham Wheeler. He goes into considerable details about writing tests, defining `setUp()` and `tearDown()`. running asychoronous tests, using 
and creating matchers, and configuring your test environment.
