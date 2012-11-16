---
layout: post
title: "character codes and strings in Dart"
date: 2012-11-15 15:29
comments: true
categories: [charCodes, charCodeAt, StringBuffer]
---

In my Python programs, I found use for the `ord()` and `chr()` builtins. `ord()` takes a character as an argument
and returns its ASCII value; `chr()` takes an ASCII value as an argument and returns the corresponding string character (these
are rough descriptions; you can read more about these builtins [here]("http://docs.python.org/2/library/functions.html#ord")).

Similar tools exist in Dart. To get a list of character codes for a string, use `charCodes`:
    String s = "hello";
    print(s.charCodes);
    // [104, 101, 108, 108, 111]
  
To get a specific character code, you can either subscript `charCodes`:
    print(s.charCodes[0]); 
  
or - this is the more common way - use `charCodeAt`:
    print(s.charCodeAt(0));
  
To assemble a string from a list of character codes, use the `String` factory, `fromCharCodes`:
    List<int> charCodes = [104, 101, 108, 108, 111];
    print(new String.fromCharCodes(charCodes));
    // "hello"

  
If you are using a StringBuffer to build up a string, you can add individual charCodes using `addCharCode`
(use `add()` to add characters; use `addCharCode()` to add charCodes):

    StringBuffer sb = new StringBuffer();
    charCodes.forEach((charCode) {
      sb.addCharCode(charCode);
    });
  
    print(sb.toString());
    // "Hello" 


Here is an implementation of the `rot13` algorithm, using the tools described above. `rot13` 
is a simple letter substitution algorithm that rotates a string by 13 places by replacing each
character in it by one that is 13 characters removed ('a' becomes 'n', 'N' becomes 'A', etc.):

    String rot13(String s) {
      List<int> rotated = [];
      
      s.charCodes.forEach((charCode) {
        final int A = 65;
        final int a = 97;
        
        if ([A, a].some((item){
          return item <= charCode && charCode < item + 13;
          })) {
          rotated.add(charCode + 13);
        } else {
          rotated.add(charCode - 13);
        }
      });
      
      return (new String.fromCharCodes(rotated));
    }

and here is the same code using a `StringBuffer`:

    String rot13(String s) {
      StringBuffer sb = new StringBuffer();
      
      s.charCodes.forEach((charCode) {
        final int A = 65;
        final int a = 97;
        
        if ([A, a].some((item){
          return item <= charCode && charCode < item + 13;
          })) {
          sb.addCharCode(charCode + 13);
        } else {
          sb.addCharCode(charCode - 13);
        }
      });
      return sb.toString();
    }

   
Running the code:
 
    var words_list = [["Jung", "be", "purely", "barf"],
                      ["aha", "nun"]];
    words_list.forEach((word_list) {
      print(word_list.map((word) {
        return rot13(word);
      }));
    });
    // ["What", "or", "cheryl", "ones"]
    // ["nun", "aha"] 
