---
layout: post
title:  "F-lang: motivations"
date:   2020-04-04 15:00:00 +0100
categories: f-lang
comments: true
---
In the [previous post] I rapidly evoked my personal project [f-lang]. As its name suggests it is a programming
language. To be honest, I am aiming only to learn new things about compilers and computers. I just don't want to use
[C++] in any new personal project and it is a good way to stay busy while waiting for [jai].

## Learning expectations
There are a lot of things that I will certainly learn with this project, first of all how compilers work even if I
already have some knowledge about that. But the most interesting points for me are hardware and system related. As my
ideal final goal is to generate a binary directly by myself, I'll have to learn some ASM and executable file format. I
also want to improve myself in writing efficient code.
An other important point in my learning vision is to do absolutely everything from scratch, so I don't use the STL or
the C runtime from the start. This forces me to learn how everything is working, starting with memory allocators and
hash-tables... This is how I started to learn at [Epitech] and I think that it will be nice to do this project in the
same way.

## Features
I'll try to make [f-lang] a self contained language, that means the description of how to build the program will be
directly described in the source files by using [compile time] features. I'll certainly do it like in [jai] by having a
just in time code execution for the compile time part.
I'll also want to provide a compiler without any dependency, so I will not use LLVM for the [back-end] of the compiler.
I think that I'll try to [boostrap] the compiler as soon as possible just to prove that the language is usable.

# Language design
At the moment I don't have a clear view of every features I'll put in the language and what will be the final syntax,
but as it is a learning project my first priority is to be able to implement the minimal set of features to have all
parts of the compiler. There are a lot of features that are in [jai] I would like to implement.

There are general things about design I am already sure of:
 - Compile all sources at once
 - Sources encoded in UTF-8
 - No linkage with C++ static libraries
 - Default initialization of variables (with option to explicitly discard)
 - Function overloading
   - Named parameters of functions
   - Default parameters values
 - Implicit Context parameter passed to all functions
   - The Context struct contains memory allocator, logger,...
   - Can be disabled to call C functions
 - Out of order declaration
 - Not object oriented
 - Templates
 - Compile time code evaluation
 - Absolutely no dependencies
   - No C runtime
   - Directly rely on Win32 APIs

## Roadmap
Here is a list of things I plan to do (not necessarily in order):
 - Lexer
 - Parser
 - [Name binding]
 - [Type inference]
 - IR (Intermediate Representation) language (eventually)
 - Optimizations
   - On AST
     - Evaluation of compile time constants
	 - Inlining
   - ASM optimizations
 - Code generation
   - C++ code
     - Certainly in one compilation unit
     - Make code parsing and AST interpretation debugging easy
	 - Make it easy to debug compile time generated code
   - X86 or X64 ASM in nasm file format
     - Intermediate step to be focused on ASM learning
   - Win32 binary file
     - I already looked at the specifications and did some tests
 - Debug info file
 - Debugger

## Current status
The lexer is working well enough for now and already detects and reports errors to the user. Even if there are still
some areas of improvement in the lexer, I started to work on the parser.
Besides only working on the code I also spend a lot of time reading articles or technical documents that are related to
this project.

## Blog
Naturally I'll post updates of my progress on this project, but I hope to find more to share with you.

[f-lang]: https://github.com/Flamaros/f-lang
[C++]: https://isocpp.org/
[jai]: https://inductive.no/jai/
[previous post]: {{ url }}/c++/2020/03/28/Why-we-should-abandon-Cpp-compilation-speed.html
[compile time]: https://en.wikipedia.org/wiki/Compile_time
[back-end]: https://en.wikipedia.org/wiki/Compiler#Back_end
[boostrap]: https://en.wikipedia.org/wiki/Bootstrapping_(compilers)
[Name binding]: https://en.wikipedia.org/wiki/Name_binding
[Type inference]: https://en.wikipedia.org/wiki/Type_inference
[IR]: https://en.wikipedia.org/wiki/Intermediate_representation
[Epitech]: https://www.epitech.eu/
