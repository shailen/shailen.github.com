---
layout: post
title: "A first app with web components using Dart and Web UI library"
date: 2012-12-31 17:54
comments: true
categories: [dartlang, webui] 
---

Its time to finally create a simple `hello world` app using web components and
the dart Web UI library.

There is already a ton of literature out there on why web components are a
tremendously good idea and I won't try to do a huge 'sell' here.  If you are
completely new to web components, I can recommend
[this really good introductory blog post by Seth Ladd](http://blog.sethladd.com/2012/11/your-first-web-component-with-dart.html)

Or, if you are impatient, here's an (almost) tweet sized summary: web components 
allow developers to encapsulate their UI elements as easily reusable components.
You can define `templates` with markup that is inert until activated later,
apply `decorators` to enhance the look of those templates, create `custom
elements` and play with the `shadow DOM`. In this little app, we will not be
tikering with `decorators` or the `shadow DOM`; we _will_ be creating
`templates` and defining our own `custom element`.

The app is called `bookstore` and you can find the code at
`https://github.com/shailen/bookstore`.

Since I am new to web components and the Dart `Web UI` library, I am going to
keep this simple. In its current iteration, the app will show the user the list
of books in the bookstore and let the user add books to the collection.
The plan is to start with something minimal and over the next few weeks and
months build something a little bit elaborate (add Authors, Publishers, more
attributes to our Book class, reviews, etc) while preserving the `one-page` feel
of the app. 

## Important Files

There are a few important files in `bookstore`'s web directory that are worth
discussing now:

`lib/models.dart` contains code for a `Book` class

`web/books.html` contains the basic markup for the app

`web/books.dart` contains the Dart code that goes with that markup

`web/book_component.html` contains the markup for our web component

`web/book_component.dart` contains the Dart code for our web component

`build.dart` helps use compile our code so that it can be run


We'll discuss each of these files in detail soon.

## lib/models.dart

We're going to be creating books. This file defines a simple `Book` class. Our
books only have 1 attribute for now, a title (I told you this was simple ;).

    library models;
    
    class Book {
      String title;
      Book(this.title);
    }

## web/books.html
Here is the entirety of teh `web/books.html` file. Consider this an entry point
into the app:

    <!DOCTYPE html>
    <html>
      <head>
        <meta charset="utf-8">
        <title>Books</title>
        <link rel="components" href="book_component.html">
      </head>
      <body>
        <h1>Books</h1>
        
        <input id="new-book" type="text" placeholder="add another title">
        <button on-click="createNewBook()">Add Book</button>
        
        <ul id="books">
          <template iterate="book in books">
            <x-book-item book="{{ book }}"></x-book-item>
          </template>
        </ul>
          
        <!-- this is the non web-component way to create the <li>s
        <ul>
          <template iterate='book in books'>
            <li>{{ book.title }}</li>
          </template>
        </ul>
        -->
          
        <script type="application/dart" src="books.dart"></script>
        <script type="text/javascript" src="https://dart.googlecode.com/svn/branches/bleeding_edge/dart/client/dart.js"></script>
        
      </body>
    </html>

A few things to notice here:

    <link rel="components" href="book_component.html">

is the way you access the contents of our web component from this file.


We create an `input` where the user enters the name of a book and an
accompanying `button`:

    <input id="new-book" type="text" placeholder="add another title">
    <button on-click="createNewBook()">Add Book</button>
      
Notice the `on-click`? That is the way we inline an event listener: when the
button is clicked, `createNewBook()` fires (we'll get to that function soon)

And finally the code that actually deals with the web component:

    <ul id="books">
      <template iterate="book in books">
        <x-book-item book="{{ book }}"></x-book-item>
      </template>
    </ul>

A few things to note here. We define a `<template>` tag; we loop over our
collection of books (stored in a variable `books` in `web/books.dart`); we
instantiate our web component (`<x-book-item></x-book-item>`) and we pass each
`book` in the loop as a template variable when we instantiate the web component.

There's a lot going on here. Make sure you understand the above paragraph!

## web/books.dart

`web/books.html` has a link to a Dart file at the bottom:

    <script type="application/dart" src="books.dart"></script>

And here is what `books.dart` looks like:

    import 'dart:html';
    import 'package:bookstore/models.dart';
    
    List<Book> books = [];
    
    // binding created auto-magically!
    void createNewBook() {
      var input = query("#new-book");
      books.add(new Book(input.value));
      input.value = "";
    }
      
    main() {
      // create some data so the page doesn't look empty
      ["War and Peace", "The Idiot", "Crime and Punishment"].forEach((title) {
        books.add(new Book(title));
      });
    } 

It is pretty straightforward stuff: we import `models.dart`, the file that
contains the `Book` class; we create a `books` variable to store our collection.
We define `createNewBook()` to add a book to `books`, and we define a `main()`
function.

This Dart file MUST contain a `main()`, even an empty one will do.  In our case,
we will add a few books to our `books` collection, so that there is some data to
display.

## web/book_component.html
This contains the code that defines our web component:

    <!DOCTYPE html>
    <html>
      <body>
        <element name="x-book-item" constructor="BookComponent" extends="li">
          <template> 
            <li>{{ book.title }}</li>
          </template>
          
          <script type="application/dart" src="book_component.dart"></script>
        </element>
      </body>
    </html>

A few things to understand here:  we create a custom `<element>`; we give it a
name, `x-book-item` (all element names must begin with an `x-`); we call a
constructor, `BookComponent` (defined in `web/book_componenet.dart`, we'll get
to that file shortly) and we declare that our custom element `extends` and `li`.

Inside our `<element>`, we create a `<template>` that stores the `<li>` that
contains a book's title.

And finally, we link to the accompanying Dart file, `book_component.dart`.

## web/book_component.dart

Here we get to define our `BookComponent` class (remember that we declared that
the `<element>` we created in `book_componenet.html` use this constructor?):

    import 'package:web_ui/web_ui.dart';
    import 'package:bookstore/models.dart';

    class BookComponent extends WebComponent {
      Book book;
    }

`BookComponent` extends `WebComponent` (this is the only option for subclassing
at the moment; that may change in the future) and contains a `book` attribute.
That's it.

## build.dart

    import 'package:web_ui/component_build.dart';
    import 'dart:io';

    void main() {
      build(new Options().arguments, ['web/books.html']);
    }

To actually build the project, `run` `build.dart` (this will create an `out`
directory); then `run`  `web/out/books.html`.

## pubspec.yaml

The app only has a single `pub` dependency, `web_ui`:
        
    name: bookstore
    description: A sample app to demonstrate the use of a web component

    dependencies:
        web_ui

## Summary

This is a fair bit of code for a simple hello-world caliber app. Is all this web
component stuff really necessary, or is it overkill?  

We're just starting out, so this may seem like too much of a production given
what the needs of our app. But we have already established a
pretty important development principle: our UI elements can be nicely
ENCAPSULATED (!) and then used as necessary. We have taken the first baby steps
towards creating a widget that displays the content of each book. As our
application grows in complexity, our 'widget' will become more elaborate and we
will want to use it in all sorts of different contexts in our app. A composable,
encapsulated UI component - a web component - that can be instantiated with
varying arguments will then prove to be quite useful.





