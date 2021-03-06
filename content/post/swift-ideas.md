---
aliases:
  - /posts/swift-ideas.html
date: 2014-08-27
title: Some ideas for projects in Swift
headline: If you're looking for a cool library to build
---


If you are looking for some programming projects to port to Swift, my list is way bigger than I can tackle. Most ideas here are ports of libraries from other languages, some are different. I would love to build all of these, but can't possibly do even ten percent of this list. The list is in random order.

* An **SVG Parser** that can parse SVG, turn it into an abstract syntax tree, and has support to render it.
* A full version of [QuickCheck](https://hackage.haskell.org/package/QuickCheck-2.4.2). I created a very rough [example](http://chris.eidhof.nl/posts/quickcheck-in-swift.html)
* A full wrapper of Core Image, see [our book](http://www.objc.io/books/) or [my presentation](https://speakerdeck.com/chriseidhof/functional-swift-1) for the basic idea.
* A routing library that takes enums and builds URLs, like described [here](http://chris.eidhof.nl/posts/typesafe-url-routes-in-swift.html) and [here](https://github.com/AshFurrow/Moya), but independent of any networking framework.
* Some functional wrappers around UIControls. Not sure how this would work, maybe something like [tangible values](http://conal.net/papers/Eros/)?
* A drawing library like [diagrams](http://projects.haskell.org/diagrams/)
* A variant of [Hoogle](http://www.haskell.org/hoogle/) for Swift. This would allow you to find Swift functions by their type, not by name. Should work together with the standard libraries, and eventually also with CocoaPods.
* A web framework (like Rails or like Sinatra). Some people have already been experimenting in this direction.
* A simple implementation of [Mustache](http://mustache.github.io). The [Javascript version](https://github.com/janl/mustache.js/blob/master/mustache.js) is short (under 600 lines).
* A wrapper around GCD. For example, it's possible to write a `parallel` function that executes a bunch of functions in parallel: [gcd.swift](https://gist.github.com/chriseidhof/c62e45554c2394bb6871).
* A port of [Software Transactional Memory](http://www.haskell.org/haskellwiki/Software_transactional_memory)
* A parser/pretty-printer combinator library, maybe like [Boomerang](http://hackage.haskell.org/package/boomerang). It would generate both a parser and a pretty-printer from a single grammar.
* A library for binary serialisation (like [cereal](http://hackage.haskell.org/package/cereal))
* A library for generating or processing XML (maybe built on top of NSXMLParser?)
* A library for dealing with HTML (like [tagsoup](http://hackage.haskell.org/package/tagsoup) or [nokogiri](http://nokogiri.org))
* A library for generating PDFs in a combinatoric way (like [Prawn](https://github.com/prawnpdf/prawn), but built on top of Cocoa goodness)
* A native Markdown parsing library (or really good wrapper around [sundown](https://github.com/vmg/sundown) or another C library)

<br/> If you are starting one of these and would like feedback, ping me!

