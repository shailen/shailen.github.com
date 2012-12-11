---
layout: post
title: "Randomness in Dart: nextBool(), nextInt(), and nextDouble()"
date: 2012-12-10 14:36
comments: true
categories: 
---

The `Random` class in `dart:math` contains 3 methods, `nextBool()`, `nextInt()`
and `nextDouble()`. To use `Random`, you will need a `import 'dart:math` in your
code.

## nextBool()
simply returns `true` or `false` at random. Here is a little
function, `randUpcase()` that demonstrates `randBool()` use. `randUpcase()`
takes a string as an argument, and converts each character in the string to
uppercase if random.nextBool() is `true` and leaves it untouched if it is
`false`.

    String randUpcase(String s) {
      Random random = new Random();
      List<String> chars = s.split('');
      return Strings.join(chars.map(
        (char) => random.nextBool() ? char.toUpperCase() : char), '');
    }
    
And here is some sample usage:

    var herbs = ["parsley", "sage", "rosemary", "thyme"];
    print(herbs.map((herb) => randUpcase(herb))); // [pARslEY, SaGE, roSEMaRY, tHYME]
 
## nextInt()
takes a `max` int argument and generates a positive int between 0 (inclusive) and `max`, exclusive.

To generate a random int within a range, you can do something like this:

    int randRange(int min, int max) {
      var random = new Random();
      return min + random.nextInt(max - min);
    }
 
You can also use `nextInt()` to randomly shuffle a list (using the familiar
Fisher-Yates-Knuth algorithm):

    List shuffle(List items) {
      var random = new Random();
      for (var i = items.length - 1; i > 0; i--) {
        var j = random.nextInt(i);
        var temp = items[i];
        items[i] = items[j];
        items[j] = temp;
      }
      return items;
    }

You can use it like this:

    var items = ["fee", "fi", "fo", "fum", "smell", "Englishman"];
    print(shuffle(items)); // [fo, smell, fum, Englishman, fee, fi]

## nextDouble()
generates a random floating point value distributed between 0.0
and 1.0. Here is a little function to simulate a biased coin toss; the `percent`
argument alters the percent a coin will return heads ('H'). This is pretty much cribbed from
[this discussion on Stack Overflow](http://stackoverflow.com/questions/477237/how-do-i-simulate-flip-of-biased-coin-in-python):

    String flip(num percent) {
      Random random = new Random();
      return random.nextDouble() < percent ? 'H' : 'T';
    }

And here is some code to test that it works:

    int n = 1000;
    int heads = 0;
    for (var i = 0; i < 1000; i++) {
      if(flip(.20) == "H") heads++;
    }
    print(heads/n); // 0.209, 0.196, etc.
