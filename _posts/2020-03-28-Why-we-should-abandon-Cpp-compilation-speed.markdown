---
layout: post
title:  "Why we should abandon C++: compilation speed"
date:   2020-03-28 15:00:00 +0100
categories: C++
comments: true
---
Wellcome to my new blog about programming, I want to start it with a collection of posts about major issues of [C++][1]. My
goal isn't to rant about this language that I've been using professionally for 12 years, but to stay objective while
having eyes wide open on other things that exist.

The compilation speed is not a direct issue, but a consequence of some root design errors.

## Why this is so important?
If you decide to use a system programming language, it is certainly because you are aiming to get the maximum
performances of the hardware your software will run on. Otherwise you'll certainly not choose [C++][1] that is known to be
old and complicated. If performances aren't in your top priorities, some other languages with a more general purpose
like [C#][2] or [Java][3] are simpler to use and offer a decent level of performance. There are also some other new languages
like [Rust][4] or [Go][5] that may have more restricted scopes.

So it seems natural for me to expect that [C++][1] compilers that are developed in this language show its capacities in
term of performances. Today this is deceptive to see that most compilers aren't multi-threaded out of the box. It simply
give a bad image to developers that will potentially choose this language.

The second and main raison why the compilation time is something critical is the impact on your productivity. Compilers
actually are so slow that a lot of [C++][1] developers use particular patterns and techniques to get a good level of
productivity. They prefer to limit themselves on features that they can use to avoid getting into situations where
it can take literally hours to build an application (sometimes with less than 2 millions of lines of code).
Using idioms like [PIMPL][6] and other techniques that also impact the quality of generated code are bad options in my
opinion. If you aren't familiar with any kind of techniques that help [C++][1] developers keep compilation times at
reasonable amounts of time, you can read this post about [physical design][16] (this link is a copy of the original [post][7] from Our Machinery [website][17] which is now dead).

## How can C++ improve the situation
The feature of [C++20 modules][8] should improve the situation significantly, but sadly it will request a major effort to
use it with existing projects. Another thing will be the possibility to give multiple source files to the compiler
directly which will give a result like [unity builds][9] but standardized, this particular technique is also interesting
because it also improves the quality of generated code, but sadly compilers tend to consume a lot of RAM and out of
memory exceptions are frequent.

## Advice
It is a very good idea to start a new [C++][1] project with compilation speed in mind. Use the most simple techniques like
[forward declaration][10], take care of not creating new source files for too few lines of code, be especially cautious with
templates and meta-programmation, initializer lists (and other very recent features)...

On my personal project [f-lang][11], I recently rewrote a part of my code to remove all dependencies to the STL, and my
compilation time has dropped from 7 seconds to less than 4 for a code that has more features. 4 seconds is still an
tremendous amount of time to build an application that has just 5,000 lines of codes (including comments and empty
lines). Just take a look at what some games can display today. How can we imagine that it's so complicated to analyse
text files and generate a binary? You should compare this number to what [D1][12] was capable of, about 400,000 lines per
second. And [Jonathan Blow][13] targets 1,000,000 with his [jai][14] language.

## Last word
I recently find a good [presentation][15] of all techniques that can be used to reduce the compilation time.

[1]: https://isocpp.org/
[2]: http://csharp.net/
[3]: https://www.java.com/en/
[4]: https://www.rust-lang.org/
[5]: https://golang.org/
[6]: https://en.cppreference.com/w/cpp/language/pimpl
[7]: https://ourmachinery.com/post/physical-design/
[8]: https://isocpp.org/files/papers/p1103r2.pdf
[9]: https://en.wikipedia.org/wiki/Single_Compilation_Unit
[10]: https://en.wikipedia.org/wiki/Forward_declaration
[11]: https://github.com/Flamaros/f-lang
[12]: https://digitalmars.com/d/1.0/index.html
[13]: https://en.wikipedia.org/wiki/Jonathan_Blow
[14]: https://inductive.no/jai/
[15]: https://fr.slideshare.net/corehard_by/the-hitchhikers-guide-to-faster-builds-viktor-kirilov-corehard-spring-2019
[16]: https://www.gamedeveloper.com/programming/physical-design-of-the-machinery
[17]: https://ourmachinery.com/
