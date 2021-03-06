---
aliases:
  - /posts/functional-view-controllers.html
date: 2015-01-13
title: Functional View Controllers
headline: Wrapping View Controllers in a Functional Way
---


I'm working on something new: a functional wrapper around view controllers. Making view controllers simpler has been a [long obsession](http://www.objc.io/issue-1/) for me. I think functional view controllers might make them even simpler. This is still very much work in progress, but I hope that over the next months I can flesh out something nice. For this article, we'll work with artists and albums, and this is how they're defined:

    struct Album {
        let name: String
    }
    
    struct Artist {
        let name : String
        let albums: [Album]
    }

And here's some example data:
    
    let artists : [Artist] = [
        Artist(name: "JS Bach", albums: [Album(name: "The Art of Fugue")]),
        Artist(name: "Simeon Ten Holt", albums: [])
    ]

When working with UIKit, view controllers are the opposite of isolated: they are connected to everything. The view controller often pulls network or database data, populates and interacts with views, and finally, it pushes other view controllers onto the navigation stack (or presents them in a different way). In this article, we'll look at view controllers that do only one thing: they populate the views and interact with them. They don't load any data themselves, nor do they present other view controllers.

In functional view controllers, every view controller takes some input, renders that, and has some output. For example, if we consider the stock Music app, there's a simple table view controller. It displays a list of artists, and, when the user taps on an artist, it shows another view controller. We could model that as following:

    let chooseArtist: ViewController<[Artist], Artist>

For now, we can ignore how it is implemented. The only thing this view controller does is displaying the list of artists in a table, and calling a callback once an artist is selected. We can also make a `chooseAlbum` view controller, which, given a list of albums, renders them and lets the user select an album:

    let chooseAlbum: ViewController<[Album],Album>

Now, we would like to present both in a navigation controller. To do that, there's a function called `rootViewController`, which lifts a view controller into a navigation controller:

    func rootViewController<A,B>(vc: ViewController<A,B>) -> 
                               NavigationController<A,B>

We can use it like this:
    
    let startPoint: NavigationController<[Artist], Artist> = 
        rootViewController(chooseArtist)

Now, we would like to add some functionality to push another view controller. For example, when the user selects an artist, we would like to push the `chooseAlbum` view controller onto the navigation stack. To do that, we can use the `>>>` operator:

    func >>><A,B,C>(l: NavigationController<A,B>, 
                    r: ViewController<B,C>) -> 
                       NavigationController<A,C>

The operator takes a left operand (a navigation controller from A to B) and a right operand (a view controller from B to C). When the left view controller is done (e.g. when an artist is selected) it pushes the right view controller.

However, we can't use this `>>>` operator yet. Our `chooseArtist` returns an artist, but our `chooseAlbum` expects a list of albums, not just a single artist. Luckily, the solution is very easy: we can use a `map` function (which works similar to `map` on optionals or dictionaries).

    func map<A,B,C>(vc: NavigationController<A,B>, 
                    f: B -> C) -> 
                    NavigationController<A,C>

Basically, the map function takes a navigation controller that maps from A to B. Once that navigation controller is done, it applies a function `f` to convert that B into a C, and we end up with a navigation controller that maps from A to C. Now, we have all the ingredients to write down our entire hierarchy:

    let artists = rootViewController(chooseArtist)
    let navigation = map(artists, { $0.albums }) >>> chooseAlbum

We're almost done. We haven't looked at how `chooseArtist` and `chooseAlbum` are defined. They're both table view controllers. There's a simple function `tableViewController` in the library which will render an array of things into a table, and performs an action when a cell is selected. Because all of this can be made to work independent of the datatype, the only thing we need to specify is how to configure the cell:

    func tableViewController<A>(render: (UITableViewCell, A) -> ()) -> ViewController<[A],A>

    let chooseArtist: ViewController<[Artist], Artist> = tableViewController { cell, artist in
        cell.textLabel?.text = artist.name
    }
    
    let chooseAlbum: ViewController<[Album],Album> = tableViewController { cell, album in
        cell.textLabel?.text = album.name
    }

Now that we have our individual view controllers defined, and our navigation stack, it's time to setup the window's root view controller in the app delegate. We run the navigation stack we created with the list of artists defined above:

    window?.rootViewController = run(navigation, artists) { album in
        println("Selected \(album.name)")
    }

That's all there is to it. With just a few simple lines, we have created a working app with two table views, with the only "real" programming that we've done is configuring the cell. Based off of that, we created two table view controllers, and hooked them up together. The table view controllers know absolutely nothing about each other, and are not contaminated with any navigation controller logic. They don't even know how to load the data. Having to do this outside of the view controller really forces you to separate concerns.

The current implementation is a proof of concept. We'll have to see how this can extend. For example, how does this work with custom transitions? Adding modal transitions is easy, but I'm not entirely sure what would be the best way to have completely custom transitions outside of a navigation controller. It's very easy to use this technique with existing view controllers, you can easily wrap them. I'd love to experiment more with this. But as I'm mostly doing Mac development these days, I can't promise the development will go very fast.

A full example is on [github](https://github.com/chriseidhof/functional-view-controllers/).

