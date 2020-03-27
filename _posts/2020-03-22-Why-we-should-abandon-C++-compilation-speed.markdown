---
layout: post
title:  "Why we should abandon C++: compilation speed"
date:   2020-03-22 20:37:00 +0100
categories: C++
github_comments_issueid: 
---
Welcome to my new blog about programming, I want to start it with a collection of posts about major issues of C++.
My goal isn't to clash this language that I use professionaly since 12 years, but to stay objective while having eyes wide open on other things that exists.

The compilation speed is not a direct issue, but a consequence of some root design errors.

## Why this is so important?
If you decide to take a system programming language this is certainly because you are targetting to get the maximum performances of the hardware on which
your software will run. You'll certainly not choose the C++ that is known to be old and complicated if the performances aren't in your top priorities, some other
language with a more general purpose are simple to use and offer decent performances like [C#] or [Java]. There is also some other new language like [Rust] or [Go] with
a maybe little more restricted scope of usages.

So it seems natural for me to expect that [C++] compilers that are developed in this language show his capacities in term of performances. Today this is deceptive to
see that most compilers aren't multi-threaded out of the box. It simply give a bad image to developers that will potentialy choose this language.

The second and main raison of why the compilation time is something critical is the impact on our productivity. Actually compilers are so slow that a lot [C++]
developers use particular patterns and techniques to get a good level of productivity. They prefer to limit themselves on features that they can use to avoid
falling in situations where compilations can take literally hours to build an application (sometime with less than 2 millions of lines of code).
Using idioms like [PIMPL] and others techniques that also impact the quality of generated code is something we should not tolerate in my opinion.
If you aren't familiar with kind of techniques that help C++ developer to keep compilation time do reasonable amout of time, you can read this post about [physical design].

## How C++ can improve the situation
C++20 modules feature should improve the situation significantly, but sadly it will request a major effort to enable it on existing projects.
I don't think that it will be easy to add [precompiled headers] to standard, but modules are already a kind of but more evoluted. A other thing will the possibility
to give multiple source files to the compiler directly which will give a result like [unity builds] but standardized, this particular technique is also interesting
because it also improve the quality of generated code, but sadly compilers tends to consumme a lot of RAM and out of memory crash are frequent.

## Advices
It a very good thing to start a new C++ project with compilation speed in mind, use the most simple techniques like [forward declaration], take care of not creating
source files for to few lines of code, be particularly cautious with templates and meta-programmation, initililization list (and other to modern features),...

On my personnal project [f-lang], I recently rewrite a part of my code to remove all dependencies to the STL, and my compilation time has dropped from 7 seconds to less than 4
for a code that has more features. 4 seconds is still an infinite amount of time to build an application that have just 5,000 lines of codes (including comments and empty lines).
Just take a look at what somes game can display today, how we can imagine that it is so complicated to analyse text files and generate a binary?
You should compare this number to those the first version of [D] also called [D1] that is capable to build 400,000 lines per second and [Jonathan Blow] target 1,000,000 with his language
[jai].

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