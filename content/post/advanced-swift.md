---
aliases:
  - /posts/advanced-swift.html
date: 2015-07-21
title: Advanced Swift
headline: Our New Book
---


Over the last months, [Airspeed Velocity](http://airspeedvelocity.net) and me have been very busy writing a new book: [Advanced Swift](http://www.objc.io/books/advanced-swift/). We are quite far along, most of the book has been written already. During the next months, we will polish the chapters and release them one by one. We aim to have the book done by this fall (hopefully so we can coincide with the official Swift release).

Writing this book has been a lot of fun, and a huge learning experience. After finishing [my first book](http://www.objc.io/books/fpinswift/) last summer, I immediately started thinking about other possible books to write. When meeting Airspeed in London, we started talking about a possible collaboration, came up with a list of subjects and started writing. For this book, we also did something different: we asked [Ole Begemann](http://oleb.net) as our technical reviewer. He is very thorough and has already helped shaped the book in some crucial parts.

I personally had a lot of fun figuring out the C interoperability. I believe this will be a huge topic in the community, especially once Swift is open source. Many of the Cocoa libraries will not be available under Linux, and rather than writing every possible library from scratch, it will be much easier to write a Swift layer on top of them. Therefore, after the announcement that Swift will be open source, we decided to focus more on interoperability in the book.

In our book, we will wrap two libraries: [cmark](https://github.com/jgm/cmark), a library for parsing CommonMark, and [libuv](https://github.com/libuv/libuv), a library for doing asynchronous I/O. When wrapping a C library, the two most difficult things are conversions between types (for example, converting between a C data buffer and `NSData`), and memory management (because every C library deals with memory in a different way).

When wrapping a synchronous library (such as cmark), we can often make memory management really easy by wrapping pointers in classes, and then using the `deinit` method to free up any memory. When wrapping asynchronous code, this becomes a bit harder, because classes might go out of scope (and thus are deallocated) before the memory needs to be freed. 

We will also discuss C function pointers, and how to work with them. Because C function pointers do not allow for capturing of any context (e.g. refer to variables outside of a function), we will need some special techniques to convert C pointer-based APIs into APIs that work with regular Swift closures.

The interop chapters are only a small part of the book, we will cover a lot of topics in detail: collections, generics, structs and classes, strings, errors, protocols and much more. If you want to start reading already, and help shape the book, you should get into the [early access program](http://www.objc.io/books/advanced-swift/) today.

