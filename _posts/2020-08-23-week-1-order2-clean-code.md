---
layout: post
title: Clean code - Aug 17 - 23
categories:
    - book
excerpt_separator:  <!--more-->
---

## Comments

Don’t comment bad code—rewrite it.

Most of the time, comments should be ignored.

The proper use of comments is to compensate for our failure to express ourself in code.

Code are always updated, but comments aren't. The older a comment is, and the farther away it is from the code it describes, the more likely it is to be just plain wrong.

Inaccurate comments are far worse than no comments at all.

Only the code can truly tell you what it does. It is the only source of truly accurate information.

Clear and expressive code with few comments is far superior to cluttered and complex code with lots of comments.

Time spent writing comments to explain the mess you've made is better spent cleaning that mess.

The only truly good comment is the comment you found a way not to write.

Sometimes it is useful to warn other programmers about certain consequences. A comment may be used to amplify the importance of something that may otherwise seem inconsequential.

## Functions

The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that.

Functions should hardly ever be 20 lines long.

Blocks within `if` statements, `else` statements, `while` statements and so on should be one line long. Probably that line should be a function call. Not only does this keep the enclosing function small, but it also adds documentary value because the function called within the block can have a nicely descriptive name. This means, the indent level of a function should not be greater than one or two. This will improve readability. 

Functions should do one thing. They should do it well. They should do it only.

One way to know that a function is doing more than "one thing" is if you can extract another function from it with a name that's is not merely a restatement of its implementation. Function that's doing one thing cannot be reasonably divided into sections.

Mixing levels of abstraction within a function is always confusing. (this wouldn't be a problem if more functions are created to abstract the logic into its own method)

The Stepdown rule: we want the code to read like a top-down narrative. We want every function to be followed by those at the next level of abstraction so that we can read the program, descending one level of abstraction at a time as we read down the list of functions. It is an effective technique for keeping the abstraction level consistent.

My general rule for `switch` statements is that  they can be tolerated if they appear only once, are used to create polymorphic objects, and are hidden behind an inheritance relationship so that the rest of the system can't see them

“You know you are working on clean code when each routine turns out to be pretty much what you expected.” Half the battle to achieving that principle is choosing good names for small functions that do one thing. The smaller and more focused a function is, the easier it is to choose a descriptive name.

A long descriptive name is better than a long descriptive comment.

Be consistent in your names. Use the same phrases, nouns, and verbs in the function names you choose for your modules.

The ideal number of arguments for a function is zero (niladic). Next comes one (monadic), followed closely by two (dyadic). Three arguments (triadic) should be avoided where possible. More than three (polyadic) requires very special justification—and then shouldn’t be used anyway.

Flag arguments are ugly. Passing a boolean into a function is a truly terrible practice. It immediately complicates the signature of the method, loudly proclaiming that this function does more than one thing. It does one thing if the flag is true and another if the flag is false!

Make sure function name describe exactly what it does (no side effects)

If it does more thing, consider breaking function into functional pieces since each function should **Do one thing**

In general output arguments should be avoided. If your function must change the state of something, have it change the state of its owning object.

Functions should either do something or answer something, but not both.

Prefer exception over returning error code

Dijkstra said that every function, and every block within a function, should have one entry and one exit. There should only be one return statement in a function, no break or continue statements in a loop, and never, ever, any goto statements.

So if you keep your functions small, then the occasional multiple return, break, or continue statement does no harm and can sometimes even be more expressive than the single-entry, single-exit rule.

Writing software is like any other kind of writing. You get your thoughts down first, then you massage it until it reads well. The first draft might be clumsy and disorganized, so you wordsmith it and restructure it and refine it until it reads the way you want it to read. It's **okay** to start your functions long and complicated, it's **okay** to have lots of indenting and nested loops, it's **okay** to have arbitrary names, duplicate code. Just make sure you have unit tests that covers every one of those clumsy lines of code. Then you start massage and refine that code with all the good practices mentioned above. In the end, you wind up with functions that follows the rules laid down in this chapter. Code almost never comes out elegant in your first try, I don't think anyone could. When put down logic, it's the logic that you are focusing on. Once functionalities are down, then you focus on how to make the code elegant following good practices.

Master programmers think of systems as stories to be told rather than programs to be written.
This chapter has been about the mechanics of writing functions well. If you follow the rules herein, your functions will be short, well named, and nicely organized. But never forget that your real goal is to tell the story of the system, and that the functions you write need to fit cleanly together into a clear and precise language to help you with that telling.

## Meaningful names

The name of a variable, function, or class, should answer all the big questions. It should tell you why it exists, what it does, and how it is used. If a name requires a comment, then the name does not reveal its intent.

Longer names trump shorter names, any any searchable name trumps a constant in code.

The length of a name should correspond to the size of its scope. (Code should break to single purpose and small sizes, so naturally we can create short names)

Avoid mental mapping with variable names like i or j or k, the professional understands that clarity is king, always use clearly intended names

Method should have verb or verb phrase names like `postPayment` `deletePage` or `save`

Accessor, mutators and predicates should be named for their value and prefixed with `get`, `set`, and `is` according to the language standard

When 

When constructors are overloaded, use static factory methods with names that describe the arguments.

`Complex fulcrumPoint = Complex.FromRealNumber(23.0);`

Pick one word for one abstract concept and stick with it.

If `get` is used in other classes to retrieve values, try not to use word like `fetch` in the newly created classes, keep it consistent. It’s confusing to have a controller and a manager and a driver in different classes of the same code base.

Our goal, as authors, is to make our code as easy as possible to understand. We want our code to be a quick skim, not an intense study.

Remember that the people who read your code will be programmers. So go ahead and use computer science (CS) terms, algorithm names, pattern names, math terms, and so forth. When there is no “programmer-eese” for what you’re doing, use the name from the problem domain. Separating solution and problem domain concepts is part of the job of a good programmer and designer.

Shorter names are generally better than longer ones, so long as they are clear. Add no more context to a name than is necessary.

## Clean Code

If we write bad code it's not others fault but our own, manager defend schedule, product manager defend features and what we do? We defend code quality with equal passion.

The only way to make the deadline, the only way to go fast is to keep the code as clean as possible at all times

> I like my code to be elegant and efficient. The logic should be straightforward to make it hard for bugs to hide, the dependency minimal to ease maintenance, error handling complete according to articulated strategy, and performers close to optimal so as not to attempt people to make the code messy with unprincipled optimizations. Clean code does one thing well. — Bjarne Stroustrup, inventor of C++ and author of The C++ Programming Language

Clean code is focused. Each function, each class, each module exposes a single-minded attitude that remains entirely undistracted, and unpolluted, by the surrounding details.

> Clean code is simple and direct. Clean code reads like well-written prose. Clean code never obscures the designer's intent but rather is full of crisp abstractions and straightforward lines of control. — Grady Booch, author of Object Oriented Analysis and Design with Applications

> Clean code can be read, and enhanced by a developer other than its original author. It has unit and acceptance tests. It has meaningful names. It provides on way rather than many ways for doing one thing. It has minimal dependencies, which are explicitly defined, and provides a clear and minimal API. Code should be literate since depending on the language, not all necessary information can be expressed clearly in code along. — "Big" Dave Thomas, found of OTI, godfather of the Eclipse strategy

> I could list all of the qualities that I notice in clean code, but there is one overarching quality that leads to all of them. Clean code always looks like it was written by someone who cares. There is nothing obvious that you can do to make it better. All of those things were thought about by the code's author, and if you try to imagine improvements, you 're led back to where you are, sitting in appreciation of the code someone left for you, code left by someone who cares deeply about the craft. — Michael Feathers, author of Working Effectively with Legacy Code

I've decided to not type more quotes here not because they are not useful, but they are and there are just too many of them. I've highlighted the meaningful ones in the book. Take a look at the book's highlights instead.