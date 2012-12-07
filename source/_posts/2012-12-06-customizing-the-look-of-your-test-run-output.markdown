---
layout: post
title: "customizing the look of your test run output"
date: 2012-12-06 14:57
comments: true
categories: [dartlang, dart, unittest]
---

`unittest` provides a default configuration that determines the
content and appearance of the output from running your tests. In addition, the
package provides fancier configurations for running tests in the browser, or on
the VM.

But suppose you want to customize the test run output? Maybe you are an ardent
Test Driven Development (TDD) hacker, and want to have your test output be
in sync with TDD Red-Green-Refactor workflow (passing tests in green, failing
tests in red); maybe you want a green "." printed to the command line for every
passing test (you don't care for all the "PASS: ..." messages that the default
configuration gives you), and a red 'F' printed for every failing test, with
more details about the failures in the summary. You also want the ouput to
list the file where the tests being run are located and you want to know how
long it takes to run all your tests.

To do all this, you need to extend the default `Configuration` class (see
`unittest/src/config.dart`) and customize your environment by passing an
instance of that class to `configure()` as an argument.
A `Configuration` has several functions that get called at different stages of
the testing process:

    onInit(), when the test framework is initialized
    onStart(), before the first test is run
    onTestStart(), when a test starts running
    onTestResult(), upon the completion of each test
    onDone(), when all tests are done

and you can override all of these.

Let's extend `Configuration` and begin by adding a few constants to help with the
presentation:

    class ColorTestRunner extends Configuration {
      const String NEUTRAL_COLOR = "\u001b[33;34m"; // blue
      const String PASS_COLOR = "\u001b[33;32m";    // green
      const String FAIL_COLOR = "\u001b[33;31m";    // red
      const String RESET_COLOR = "\u001b[33;0m";
      
      ...
    }

We will prefix our failing output with `FAIL_COLOR`, passing output
with `PASS_COLOR` and use `RESET_COLOR` to revert back to the user's settings.
We will use `NEUTRAL_COLOR` to print a small introduction and the summary.

We are now ready to start filling out our `ColorTestRunner` class. The
`onInit()` of `Configuration` is empty; let's add a little introduction that
prints the name of the file containing the tests:

    void onInit() { 
      print(NEUTRAL_COLOR);
      Options options = new Options();  
      print("Running tests for ${options.script}");
      print(RESET_COLOR);
    }

Now let's move on to `onStart()`. This is called after `onInit()` but before
the first test is run. In the default `Configuration`, this prints the 
`unittest-suite-wait-for-done` message. We pushed our custom message in
the `onInit()`, so we don't need another message here. But this would be a
good place to start running the stopwatch for timing our tests:

    Stopwatch stopwatch = new Stopwatch();
    void onStart() => stopwatch.start();

`Configuration` defines the twin `onTestStart()` and `onTestResult()` functions
that basically define which test is currently being run:

  void onTestStart(TestCase testCase) {
    currentTestCase = testCase;
  }

  void onTestResult(TestCase testCase) {
    currentTestCase = null;
  }

We don't need to tinker with `onTestRun()`, but we'll override `onTestResult()` to
output the green `.` or red `F` for passing and failing tests, respectively:

    void onTestResult(TestCase testCase) {
      var color = testCase.result == PASS ? PASS_COLOR : FAIL_COLOR;
      stdout.writeString("${color}${testCase.result == PASS ? '.' : 'F'}$RESET_COLOR");
      currentTestCase = null;
    }

The `onDone()` function is where `Configuration` does the heavy lifting: it
outputs the status and description of each test as well as the error messages
and stack traces for failing tests. Then, it provides summary informtion for
the test run. We'll just skip over the passing tests and color-code our
summary (it will be red if even a single test fails). Then, we will output
the total time taken for the tests to run (remember the stopwatch we started in
`onStart()`?.

With a little bit of refactoring, and some additional formatting added to the
output, here is what the script looks like:
    
    library colorTestRunner;
    
    import 'package:unittest/unittest.dart';
    import 'dart:io';

    /// Overrides default Configuration to provide colorful command line
    /// output when tests are run. Loosely based on RSpec (https://www.relishapp.com/rspec).
    /// Outputs green (passing) "." and red (failing) "F" characters as tests are running. 
    /// If there are failing tests, provides detailed error report in red.
    /// Provides a short summary.
    class ColorTestRunner extends Configuration {
      const String NEUTRAL_COLOR = "\u001b[33;34m"; // blue
      const String PASS_COLOR = "\u001b[33;32m";    // green
      const String FAIL_COLOR = "\u001b[33;31m";    // red
      const String RESET_COLOR = "\u001b[33;0m";
      const int BORDER_LENGTH = 80;
     
      Stopwatch stopwatch = new Stopwatch();
      
      void onInit() => _printResultHeader();
      void onStart() => stopwatch.start();
    
      void onTestResult(TestCase testCase) {
        var color = testCase.result == PASS ? PASS_COLOR : FAIL_COLOR;
        _colorPrint(color, testCase);
        currentTestCase = null;
      }
    
      void onDone(int passed, int failed, int errors, List<TestCase> testCases,
                  String uncaughtError) {
        stopwatch.stop();
    
        _printFailingTestInfo(testCases);
        _printSummary(passed, failed, errors, uncaughtError);
        print("${NEUTRAL_COLOR}Total time = ${stopwatch.elapsedMilliseconds / 1000} seconds.$RESET_COLOR");
      }
      
      void _printFailingTestInfo(List<TestCase> testCases) {
        print(FAIL_COLOR);
        for (var testCase in testCases) {
          if (testCase.result != PASS) {
            print("${testCase.result.toUpperCase()}: ${testCase.description}");
    
            if (testCase.message != '') {
              print(testCase.message);
            }
      
            if (testCase.stackTrace != null && testCase.stackTrace != '') {
              print(testCase.stackTrace);
            }
         
            print(_repeatString(".", BORDER_LENGTH));
          }
        }
        print(RESET_COLOR);
      }
      
      void _printSummary(int passed, int failed, int errors, String uncaughtError) {
        if (_passed(failed, errors, uncaughtError)) {
          print(PASS_COLOR); 
          print("All $passed tests passed.");
        } else {
          print(FAIL_COLOR); 
          if (_noTestsFound(passed, failed, errors)) {
            print('No tests found.');
          } else if (uncaughtError != null) {
            print("Top-level uncaught error: $uncaughtError");
          } else {
            print("$passed PASSED, $failed FAILED, $errors ERRORS.");
          }
        }
      }
      
      bool _noTestsFound(int passed, int failed, int errors) {
        return passed == 0 && failed == 0 && errors == 0;
      }
    
      bool _passed(int failed, int errors, String uncaughtError) {
        return failed == 0 && errors == 0 && uncaughtError == null;
      }
      
      void _printResultHeader() {
        print(NEUTRAL_COLOR);
        Options options = new Options();  
        String description = "Running tests for ${options.script}";
        String frame = _repeatString("=", description.length);
        print(frame);
        print(description);
        print(frame);
        print(RESET_COLOR);
      }
    
      String _repeatString(String str, int times) {
        StringBuffer sb = new StringBuffer();
        for (var i = 0; i < times; i++) {
          sb.add(str);  
        }
        return sb.toString();
      }
      
      void _colorPrint(String color, TestCase testCase) {
        stdout.writeString("${color}${testCase.result == PASS ? '.' : 'F'}$RESET_COLOR");
      }
    }
    
    /// The function that should be called right before the tests.
    void useColorTestRunner() {
      configure(new ColorTestRunner());
    }

To use this script, call the `useColorTestRunner()` just before where you have
defined your tests. Here is a simple use case:

    import 'package:unittest/unittest.dart';
    import '<path to>/color_test_runner.dart';
    
    num factorial(num n) {
      if (n < 2) {
        return 1;
      } else {
        return n * factorial(n - 1);
      }
    }
    
    void main() {
      useColorTestRunner(); // we're using our own configuration
      
      test("when n is 0", () {   
        expect(factorial(0), equals(1));
      });
      
      test("when n is 1", () {
        expect(factorial(1), equals(1));
      });
      
      test("when n is > 1", () {
        expect(factorial(5), equals(120));
      });
    }

Call this from the command line: `dart <test file>.dart` and your output should
look like this:

{% img /images/passing_tests.png %}

Everything is green. Great! But perhaps you were in a hurry and got your `0` and
`1` mixed up in the first test:

      test("when n is 0", () {   
        expect(factorial(1), equals(0)); // oops....!
      });
      
If you run the tests now, you'll see:

{% img images/failing_tests.png %}
