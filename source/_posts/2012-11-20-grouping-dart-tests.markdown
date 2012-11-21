---
layout: post
title: "grouping dart tests"
date: 2012-11-20 16:02
comments: true
published: true
categories: 
---

Yesterday, we create a simple Dart package with a `range` library and wrote a couple of tests. Let's pick up
where we left off and add more tests.

We should test that the list returned by `range()` does not include `stop` and that an ArgumentError is raised when start >= stop. 
Modify your `range_test.dart` file so that it contains the new tests:

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

Run the tests again. They should all pass. We have doubled our test coverage!

Now, what we have here works fine, but we can improve things a bit. Notice the repetition in the string arguments we pass to
each `test()` ("range() produces ...", "range() throws ...", etc.)? We should clean that up. Also, we have 4 tests that fall into
2 natural groups: the first two call `range()` with valid arguments and the last two with invalid arguments. We should arrange
our tests more clearly to reflect this grouping. Finally, there are no tests for the optional `step` argument. We should add those.
Rewriting our tests, we get: 

    import 'package:unittest/unittest.dart';
    import 'package:range/range.dart';
    
    void main() {
      group("range()", () {
        group("produces a list that", () {
          test("starts at `start`", () {
            expect(range(0, 4)[0], equals(0));
          });
    
          test ("stops before `stop`", () {
            expect(range(0, 4).last, equals(3));
          });
    
          test("has consecutive values if no `step` is given", () {
            expect(range(1, 6), equals([1, 2, 3, 4, 5]));
          });
    
          test("has non-consecutive values with `step` > 1", () {
            expect(range(1, 6, 2), equals([1, 3, 5]));
          });
        });
    
        group("throws an exception when", () {
          test("start > stop", () {
            expect(() => range(5, 2), throwsA(new isInstanceOf<ArgumentError>()));
          });
    
          test("start == stop", () {
            expect(() => range(5, 5), throwsA(new isInstanceOf<ArgumentError>()));
          });
        });
      });
    }

Much better. We use nested `group()`s to organize our tests; we pass descriptive string args to each `group()` to make our intent clear; we get rid
of a lot of repetition. 

Let's run our tests again:

    unittest-suite-wait-for-done
    PASS: range() produces a list that starts at `start`
    PASS: range() produces a list that stops before `stop`
    PASS: range() produces a list that has consecutive values if no `step` is given
    PASS: range() produces a list that has non-consecutive values with `step` > 1
    PASS: range() throws an exception when start > stop
    PASS: range() throws an exception when start == stop
    
    All 6 tests passed.
    unittest-suite-success
