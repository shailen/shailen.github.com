---
layout: post
title: "Running Only Some of Your Dart Tests: using solo_test() and filterTests()"
date: 2012-12-07 10:00
comments: true
categories: [dartlang, dart, unittest, argparse]
---
The Dart `unittest` library allows you to run just a single test; to do so,
just change the call for that test form `test()` to `solo_test()`. 

Another way to run a subset of your tests is by using the `filterTests()`
function. `filterTests()` takes a String or a RegExp argument and matches
it against each test description; if the description matches, the test 
runs, otherwise, it doesn't. 

Before you use `filterTests()`, you need to disable the automatic running of
tests (set `autoStart` to false) and ensure that the your configuration is
initialized.

You can do this by creating a custom configuration:

    import "package:unittest/unittest.dart";
    import "package:args/args.dart";
    
    class FilterTests extends Configuration {
      get autoStart => false;
    }
    
    void useFilteredTests() {
      configure(new FilterTests());
      ensureInitialized();  
    }
    

Then, you can call `useFilteredTests()` in your `main()`, define all your
tests, call `filteredTests()` with a string or regexp argument and run your
tests using `runTests()`:

    void main() {
      useFilteredTests();

      // your tests go here

      filterTests(some_string_or_regexp);
      runTests();
    }


Here is a little working example:

    void main() {
      useFilteredTests();
      
      test("one test", () {
        expect(1, equals(1));
      }); 
      
      test("another test", () {
        expect(2, equals(2));
      });
      
      test("and another", () {
        expect(3, equals(3));
      });
      
      filterTests('test');
      // filterTests('another');
            
      runTests();
    }

`filterTests('test')` will run the first 2 tests; `filterTests('another')` will
run the last 2 tests.


It is easy to make this _considerably_ more useful by getting the argument to
`filterTests()` from the command line. That way, you can control what subset of
tests to run without having to change the code in your test suite. Here is a
slightly expanded version of the same example: 

    void main() {
      useFilteredTests();
      ArgParser argParser = new ArgParser();
      Options options = new Options();
      ArgResults results = argParser.parse(options.arguments);
      List<String> args = results.rest; // get the args from the command line
       
      test("one test", (){
        expect(1, equals(1));
      }); 
      
      test("another test", (){
        expect(2, equals(2));
      });
      
      test("and another", (){
        expect(3, equals(3));
      });
      
      // we add a group() to show how we can easily run just the tests
      // contained in it
      group("foo", () {
        test("this", (){
          expect('bar', equals('bar'));
        }); 
        
        test("that", (){
          expect('baz', equals('baz'));
        });
      });
      
      if (!args.isEmpty) {
        filterTests(args[0]);
      }
      runTests();
    }

You can run the tests from the command line by using the 
    `dart name_of_file.dart [keyword]`

syntax. If the keyword is `this`, only one test will run. If the keyword is
`foo`, all tests in the `group()` with the description of `foo` will run.
If you do not provide a keyword, all 5 tests will run.

