---
aliases:
  - /posts/json-parsing-in-swift.html
date: 2014-07-28
title: Parsing JSON in Swift
---
headline: Safe and easy


After reading [Brent](http://inessential.com/2014/06/17/c_in_sheeps_clothing)'s and [David](https://medium.com/swift-programming/b6f4f232e35e)'s posts on parsing JSON in Swift, I really wanted to write this post. However, we've been very busy with writing a book on [Functional Programming in Swift](http://www.objc.io/books/), and I wanted to finish some other things first, so it took a while to come up with it. The full code accompanying this post is on [GitHub](https://gist.github.com/chriseidhof/4c071de50461a802874e).

In this posts I will give an outline of a parsing library for dealing with JSON in Swift in a type-safe way. I bluntly copied the example JSON from David, and it looks like this:

    var json : [String: AnyObject] = [
      "stat": "ok",
      "blogs": [
        "blog": [
          [
            "id" : 73,
            "name" : "Bloxus test",
            "needspassword" : true,
            "url" : "http://remote.bloxus.com/"
          ],
          [
            "id" : 74,
            "name" : "Manila Test",
            "needspassword" : false,
            "url" : "http://flickrtest1.userland.com/"
          ]
        ]
      ]
    ]

Now, the challenge is to convert this into an array of Swift structs:

    struct Blog {
        let id: Int
        let name: String
        let needsPassword : Bool
        let url: NSURL
    }

I'll first show the finished parsing functions, which contains two operators: `>>=` and `<*>`. They might look very foreign, but parsing the entire JSON structure is as simple as this. The rest of the article only describes library code. The parsing below works in such a way, that if the JSON is invalid (e.g. the name is missing, or id is not an integer) the entire result will be nil. There's no need for reflection or KVO, we just have a couple of simple functions and some smart ways to combine them:

    func parseBlog(blog: AnyObject) -> Blog? {
        return asDict(blog) >>= {
            mkBlog <*> int($0,"id")
                   <*> string($0,"name")
                   <*> bool($0,"needspassword")
                   <*> (string($0, "url") >>= toURL)
        }
    }
    
    let parsed : [Blog]? = dictionary(json, "blogs") >>= {
        array($0, "blog") >>= {
            join($0.map(parseBlog))
        }
    }

So, what does the above code do? Let's go over the most important functions. First, let's have a look at the `dictionary` function. It's a function that, given a dictionary from `String` to `AnyObject`, tries to find a dictionary with the specified key:

    func dictionary(input: [String: AnyObject], key: String) ->  [String: AnyObject]? {
        return input[key] >>= { $0 as? [String:AnyObject] }
    }

For example, in the example JSON we expect the key "blogs" to contain a dictionary. If the dictionary exists, the above function returns it, otherwise it returns `nil`. We can write similar functions for arrays, strings, and integers (here are just their signatures, the full code is on GitHub):

    func array(input: [String:AnyObject], key: String) ->  [AnyObject]?
    func string(input: [String:AnyObject], key: String) -> String?
    func int(input: [NSObject:AnyObject], key: String) -> Int?

Now, let's have a look at the outermost structure of our JSON. It is a dictionary, with a key "blogs", which contains an array, under the key "blog". To parse that, we could have written the following code:

    if let blogsDict = dictionary(parsedJSON, "blogs") {
        if let blogsArray = array(blogsDict, "blog") {
             // Do something with the blogs array
        }
    }
    
Instead, we can define the `>>=` operator, which takes an optional value, and applies a function only if the optional is not nil. It makes use of the `flatten` function, which flattens a nested optional into a single one.

    operator infix >>= {}
    @infix func >>= <U,T>(optional : T?, f : T -> U?) -> U? {
        return flatten(optional.map(f))
    }

    func flatten<A>(x: A??) -> A? {
        if let y = x { return y }
        return nil
    }
    
The other operator that's in heavy use is the `<*>` operator. For the parsing of a single blog, we had the following code:

    mkBlog <*> int(dict,"id")
           <*> string(dict,"name")
           <*> bool(dict,"needspassword")
           <*> (string(dict, "url") >>= toURL)

You can read this as a functional call that only gets executed when all optional values are non-nil:

    mkBlog(int(dict,"id"), string(dict,"name"), bool(dict,"needspassword"), (string(dict, "url") >>= toURL))

So let's look at the definition of the `<*>` operator. It combines two optional values: as the left operand, it takes a function, and the right operand a parameter to that function. It checks if both operands are non-nil, and then just applies the function.

    operator infix <*> { associativity left precedence 150 }
    func <*><A, B>(f: (A -> B)?, x: A?) -> B? {
        if let f1 = f {
            if let x1 = x {
                return f1(x1)
            }
        }
        return nil
    }

Now you might wonder what `mkBlog` does. It is a [curried](http://en.wikipedia.org/wiki/Currying) function that wraps our initializer. First, we create a function with type `(Int,String,Bool,NSURL) -> Blog`. Then, the `curry` function turns that into `Int -> String -> Bool -> NSURL -> Blog`:

    let mkBlog = curry {id, name, needsPassword, url in 
       Blog(id: id, name: name, needsPassword: needsPassword, url: url) 
    }

This is needed so that we can use `mkBlog` together with the `<*>` operator. If we look at the first line:


    // mkBlog : Int -> String -> Bool -> NSURL -> Blog
    // int(dict,"id") : Int?
    let step1 = mkBlog <*> int(dict,"id")

We can see that combining them with `<*>` gives us a new function of type `(String -> Bool -> NSURL -> Blog)?`. And if we combine that with a string:

    let step2 = step1 <*> string(dict,"name")

We get a function of type `(Bool -> NSURL -> Blog)?`. And if we continue doing this, we end up with an optional `Blog?` value. 

I hope you can now understand how all the pieces fit together. By creating a few helper functions and operators, we can make strongly-typed JSON parsing really easy. Instead of optional, we could have also used a different type that would include errors, but that's a topic for a different blog post.

In our book, we'll be writing in much more detail about these kinds of things (and other really cool stuff). If you're interested, you can already get early access today: [Functional Programming in Swift](http://www.objc.io/books/).


