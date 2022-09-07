---
layout: post
title:  "F-lang: Generating exe file"
date:   2023-08-28 13:22:00 +0100
categories: C++
comments: true
---
## I am back

After more than two years I am back to write new posts. During this time a bought an house on which we did a lot of work and my girlfriend was pregnant.
Now I have a little boy that have almost one year. All of that took me a loot of my free time, but recently I finally was capable to work again on my project
f-lang.

So since few months I develop again on my free time and I made a little project call [Dear Time][3] to learn [Dear ImGui][4] API and restore my little routine with
something really simple. And I finally restart the development of my project f-lang. I think that I now have a decent parser, and I started to work on a backend
to generate C++ code.

I don't think I'll post on the parser because this part of the compiler is about the same level of complexity of the lexer and there is already a lot of good
enough resources on internet. Maybe the only revelant thing I did for my parser was to write from scratch an hash table module, so I did some researches about
hash functions and finally peak [Spooky V2][1] algorithm (I may consider to switch to [Google CityHash][2]).

For the C++ backend that I started, I will not do any post as I have abandonned the idea to do it. I revise my minds about it, because I finally understood that
it will force me to compose with C++ language and compiler specifities like in order declarations, calling conventions, ...
So I have finally prefered to jump back in PE file specifications to start working on a native backend which is the subject of this post. Rest assured, I won't go
into the details of ASM language for the moment, because actually I am still a newbie about it.

## Portable Executable file format
This file format is often called by its abbreviation [PE format][5] (Microsoft official documentation). PE files are called like that because this format is
architecture independant, it may contain applications targetting x86, amd_64, arm,... CPUs.

In reality [PE format][5] is just a part of Windows executable files that are still compatible with [MS-DOS][6]. Yes it is akward to keep such backward compatibility and
almost all executables generated these days only prints "This program cannot be run in DOS mode" before leaving. But if you want to do something else with the [MS-DOS][6]
part of your executable you can find a guide [here][7].

Here I will only talk about the [PE format][5] which start generally just after the [MS-DOS][6] stub. I will not enter in every details but instead I'll try to cover things
that was difficult to understand for me. Please notice that I just discovered the [PE format][5] and I may misunderstand few things.

### Loader job
The loader is a module of the kernel which is responsible to load executable file in memory and initiate its execution. We will see soon that an executable contains
different kinds of data that are organized as *sections*. You should already know *code* and *resources* sections, the first one contains program instructions while
the second one contains some assets like the program icons, configuration files,...  
The loader allocate *memory pages* and flag them before loading data of *sections* into it, it also have to correct addresses that can't be computed when generating
the executable. I'll explain this more soon.

### [Memory page][9]
A [Memory page][9] is the smallest amout of contiguous memory the CPU is capable to allocate, for x86 and x86_64 architectures this size is 4kB by default. Some CPU can be
configured by the OS to support larger sizes, but it is out of the scope of this post (follow the [Memory page][9] link for more details).  
A page can be configured by the OS depending on targetted usage, it can have rights and flags. For this post purpose only understanding that there is rights for execution,
read and write are enough.  
You should already see it coming: *code* section require execution rights while *resources* section can be mapped on pages with read only flag.

### Lexical introduction
+ [MS-DOS][6] stub: First chunk of data contained in Windows executables.
+ [ASM][8]: A symbolic representation of machine code.
+ Opcode: A value that is interpreted as a particular instruction by the CPU. This value is generally noted in hexadecimal form.
+ Code: Abreviation of "Machine code", which is a set of data that the CPU will execute without any transformation because it already a sequence of instructions.
+ VA:
+ RVA:

## Useful tools
Here is a list of some tools I used to be able to reverse engineer the executables or find errors in my generated binary.
+ [ImHex][10]: An advanced hexadecimal editor with great features for reversing.
+ [PE-bear][11]: A [PE format][5] reversing tool.
+ [PE-file-checker][12]: A tool to check validity of an executable in [PE format][5].
+ [x64dbg][13]: An x86/x64 debugger made for reverse engineering of executables (debugging without sources).

## What's next?

My next post will certainly not really soon due to the next things I have to work on for the compiler. I have to start the creation of an IR (Intermediate
Representation) and do the native code generation. This will requiere a lot of learning, taking langage design decisions (type conversions, calling conventions,...)
and improvement of existing parts of the compiler (error checking, ...).

Even if it's a learning project I want to try to do a real things with it, that why I am now planning to do a text editor and a debugger instead of trying to boostrap
the compiler. Those projects will help me to stay focus on my langage design choices to target real applications. I think that there is a lot of things can be done by a
debugger that we don't see in actual ones because of hover complicated languages ecosystem.


[1]: https://burtleburtle.net/bob/hash/spooky.html
[2]: https://github.com/google/cityhash
[3]: https://github.com/Flamaros/dear_time
[4]: https://github.com/ocornut/imgui
[5]: https://docs.microsoft.com/en-us/windows/win32/debug/pe-format?redirectedfrom=MSDN
[6]: https://fr.wikipedia.org/wiki/MS-DOS
[7]: https://osandamalith.com/2020/07/19/exploring-the-ms-dos-stub/
[8]: https://en.wikipedia.org/wiki/Assembly_language
[9]: https://en.wikipedia.org/wiki/Page_(computer_memory)
[10]: https://github.com/WerWolv/ImHex
[11]: https://hshrzd.wordpress.com/pe-bear/
[12]: https://github.com/VinnySmallUtilities/PE-file-checker
[13]: https://x64dbg.com

[jai]: https://inductive.no/jai/
