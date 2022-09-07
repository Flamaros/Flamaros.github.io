---
layout: post
title:  "Why we should abandon C++: compilation speed"
date:   2020-03-28 15:00:00 +0100
categories: C++
comments: true
---
Wellcome to my new blog about programming, I want to start it with a collection of posts about major issues of [C++]. My
goal isn't to rant about this language that I've been using professionally for 12 years, but to stay objective while
having eyes wide open on other things that exist.

The compilation speed is not a direct issue, but a consequence of some root design errors.

## Why this is so important?
If you decide to use a system programming language, it is certainly because you are aiming to get the maximum
performances of the hardware your software will run on. Otherwise you'll certainly not choose [C++] that is known to be
old and complicated. If performances aren't in your top priorities, some other languages with a more general purpose
like [C#] or [Java] are simpler to use and offer a decent level of performance. There are also some other new languages
like [Rust] or [Go] that may have more restricted scopes.

So it seems natural for me to expect that [C++] compilers that are developed in this language show its capacities in
term of performances. Today this is deceptive to see that most compilers aren't multi-threaded out of the box. It simply
give a bad image to developers that will potentially choose this language.

The second and main raison why the compilation time is something critical is the impact on your productivity. Compilers
actually are so slow that a lot of [C++] developers use particular patterns and techniques to get a good level of
productivity. They prefer to limit themselves on features that they can use to avoid getting into situations where
it can take literally hours to build an application (sometimes with less than 2 millions of lines of code).
Using idioms like [PIMPL] and other techniques that also impact the quality of generated code are bad options in my
opinion. If you aren't familiar with any kind of techniques that help [C++] developers keep compilation times at
reasonable amounts of time, you can read this post about [physical design].

## How can C++ improve the situation
The feature of [C++20 modules] should improve the situation significantly, but sadly it will request a major effort to
use it with existing projects. Another thing will be the possibility to give multiple source files to the compiler
directly which will give a result like [unity builds] but standardized, this particular technique is also interesting
because it also improves the quality of generated code, but sadly compilers tend to consume a lot of RAM and out of
memory exceptions are frequent.

## Advice
It is a very good idea to start a new [C++] project with compilation speed in mind. Use the most simple techniques like
[forward declaration], take care of not creating new source files for too few lines of code, be especially cautious with
templates and meta-programmation, initializer lists (and other very recent features)...

On my personal project [f-lang], I recently rewrote a part of my code to remove all dependencies to the STL, and my
compilation time has dropped from 7 seconds to less than 4 for a code that has more features. 4 seconds is still an
tremendous amount of time to build an application that has just 5,000 lines of codes (including comments and empty
lines). Just take a look at what some games can display today. How can we imagine that it's so complicated to analyse
text files and generate a binary? You should compare this number to what [D1] was capable of, about 400,000 lines per
second. And [Jonathan Blow] targets 1,000,000 with his [jai] language.

## Last word
I recently find a good [presentation] of all techniques that can be used to reduce the compilation time.

[C#]: http://csharp.net/
[Java]: https://www.java.com/en/
[Rust]: https://www.rust-lang.org/
[Go]: https://golang.org/
[PIMPL]: https://en.cppreference.com/w/cpp/language/pimpl
[physical design]: https://ourmachinery.com/post/physical-design/
[precompiled headers]: https://en.wikipedia.org/wiki/Precompiled_header
[unity builds]: https://en.wikipedia.org/wiki/Single_Compilation_Unit
[forward declaration]: https://en.wikipedia.org/wiki/Forward_declaration
[f-lang]: https://github.com/Flamaros/f-lang
[D]: https://dlang.org/
[D1]: https://digitalmars.com/d/1.0/index.html
[jai]: https://inductive.no/jai/
[Jonathan Blow]: https://en.wikipedia.org/wiki/Jonathan_Blow
[C++]: https://isocpp.org/
[C++20 modules]: https://isocpp.org/files/papers/p1103r2.pdf
[presentation]: https://fr.slideshare.net/corehard_by/the-hitchhikers-guide-to-faster-builds-viktor-kirilov-corehard-spring-2019