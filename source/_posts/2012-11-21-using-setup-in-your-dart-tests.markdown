---
layout: post
title: "using setUp() in your Dart tests"
date: 2012-11-21 22:47
comments: true
categories: [dart, dartlang, unittest, setup]
---

Take this minimal `Point` class:

    class Point {
      num x;
      num y;
      Point(this.x, this.y);
      
      num distanceTo(Point other) {
        ...
      }
    }

As you write tests for `Point`, you will probably want to set up one or more `point` objects that you can access in each 
test. Something like:

    void main() {
      group("Point", (){
        setUp((){
          Point p1 = new Point(3, 4);
          Point p2 = new Point(3, 5);
        });
        test("distanceTo()", (){
          expect(p1.distanceTo(p2), equals(...));
        });
      });
    }

Do this and the Editor starts complaining that it cannot make sense of `p1` or `p2`. Why?  Remember, `setUp()` simply calls the function passed
to it before each `test()` and our code defining `p1` and `p2` will therefore run every time. But (and this seems like a Captain Obvious mement) because `p1` and `p2`
are local to function called by `setUp()`, they cannot be accessed from outside that function.  But it takes only very small changes to take care of the access problem:

    void main() {
      group("Point", (){
        Point p1;
        Point p2;
        setUp((){
          p1 = new Point(3, 4);
          p2 = new Point(3, 5);
        });
        test("distanceTo()", (){
          expect(p1.distanceTo(p2), equals(34));
        });
      });
    });

Now, the function called by `setUp()` assigns a value to the already existing `p1` and `p2`.


