---
aliases:
  - /posts/blub-paradox-in-swift.html
date: 2014-08-17
title: The Blub Paradox in Swift
headline: Why all the new features look like overkill
---


In his article [Beating the Averages](http://www.paulgraham.com/avg.html), Paul Graham writes about the Blub paradox. I think this is excellent reading for every programmer, the article is full of interesting advice.

The Blub paradox is about a hypothetical language Blub, but in this article, we'll talk about Objective-C. Objective-C is a major improvement over C in terms of productivity. I wouldn't know how I would get anything done in C, coming from Objective-C. In C, there's no ARC, no KVO and you have to write a lot of boilerplate. Knowing Objective-C, and looking at a language that is more low-level, it's hard to see why you write anything except low-level code in it.

However, if you are used to Objective-C, and have never programmed in a language that allows for functional programming (such as Lisp, Haskell or Swift), it's hard to see why you would need that. Objective-C is a perfect language to express your thoughts, and most experienced iOS/Mac developers I know can translate their ideas straight into code.

Before I started Objective-C, I was writing a lot of my code in Haskell. Haskell has features like generics, pattern matching, easy recursion and first-class functions. Moving to Objective-C was very painful for me: how did people get anything done? Back then, we had to write retain/release statements, modify code in three places to define a property, and there was no easy way to define closures. Another thing I was really missing is a powerful type system that helps you think about state, input and mutability.

I started writing Objective-C full time, and after a while, I didn't miss most of those features anymore. It's perfectly possible to write beautiful code without it. I embraced the limitations and after about two years, I actually started liking Objective-C. Combined with the powerful frameworks, the iOS platform allowed me to create beautiful applications, and I focused more on the resulting product than on the programming language.

Now, with the introduction of Swift, I see a lot of confusion: if you've never used it, a type system with generics might look like overkill. Custom operators are mostly really confusing when you're not accustomed to working with them. Pattern matching looks unnecessary. Optionals are hard to wrap your head around. Functional code looks completely incomprehensible, it's much easier to read and write Objective-C.

All of these "confusing" features can help you write much more readable code. If you're not used to them, you might not see that until you thoroughly understand them. If you think Objective-C is a readable language, I couldn't agree more. However, show it to somebody who spent their entire career only writing Python, and they might be very confused by all the square brackets, retain cycles, or block syntax. The same holds for the new possibilities of Swift: if you are not used to them, it might look like nonsense. But the people who wrote Swift know what they are doing, and the new features aren't there to show off how smart they are: it's to help us write better, faster and safer code. 

If you are confused by Swift's new features, it's easy to turn that confusion into anger or frustration. I've seen this quite a bit, recently. However, instead, you could also try to turn it into curiosity. I think there's a lot to like about Swift, and I'd encourage you to dive deeper into the language and new concepts, and maybe you will like it too.
