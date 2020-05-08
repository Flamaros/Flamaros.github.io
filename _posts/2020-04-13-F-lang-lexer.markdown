---
layout: post
title:  "F-lang: Lexer"
date:   2020-05-08 15:00:00 +0100
categories: f-lang
comments: true
---
## Introduction
There is already a lot of sources that explain how compilers are working, so I will not repeat basics in my series of
posts about the development of my language f-lang. Instead I prefer to share my experiences, on what it is working and
what it is not. So I'll assume that you already have a good view of different passes that generally compose a compiler.
If not I can only recommend you to start with the [compiler definition] on Wikipedia, after that to go more in details
there is this good free book: [Crafting Interpreters]. I read only the first chapters at the moment so I can't judge
for the quality and interest of all his content, but I already learned some things from it, and I like the writing
style.

### Organization
As I work on [f-lang] on my free time, I have to stay motivated. That why I will do things in the order I will felt
motivated instead of constrain my self to make things in the most productive or critical way. Generally I like
to make things working before well structured. In that way there is few frictions with the first versions of code I
write which allows me to refine the code quickly throw multiple iterations. I try to put my focus on the
organization of data before on the code architecture to be sure that it will be easy to improve performances.

I have absolutely no experience in writing compilers when starting this project, I'll do a lot of mistakes, but this is
how we are learning. I just have to take care of being able to fix my errors. In my C++ version of the compiler I put a
lot of comments for my self to remind me how I could write things in a better way with f-lang. This will guide me on
which and how to implement features in my programming language. Remember I don't use the STL nor some C++ features to
restrict myself in a subset of C++ features that will be similar in [f-lang].

## Lexer
I made a [lexer] a few month ago for a professional project, before that I think it was at school the last time I did
one. The lexer I did for my previous company was made to "parse" C++ headers, precisely DirectX headers. I put parse
into quotes because the job of the tool was so simple that it wasn't necessary to build an [AST]. The latest
version of this lexer was capable to generate all [tokens] for more than 300,000 lines of code in only 50ms and
sincerely I think it is slow. My first attempt was running in a couple of seconds, but I was able to improve
performances really quickly. The first thing I did was to pre-allocate tokens, by using a heuristic based on
the file size, I simply considered that the average size of tokens is 5 characters. The second things I did was to use
a perfect hash table for punctuation, what I mean by perfect hash table is that the characters was directly used as
key/index without any transformation. For that I did two table one for one character punctuation and an other one
for two characters punctuation. So the lexer always have to "read" two characters even if iterating one by one. This
optimization is the one which provide the highest performance improvement. The latest optimization I did was to use a
hash table for keywords. As I didn't do much effort I think that it is possible to obtain even more speed. I didn't
look at cache-misses and branch miss-prediction issues nor at parallelization possibilities.

Actually I found a "major" issue, the code design of this lexer is based on a stack of states that makes the code hard
to maintain. Small changes can have a huge impact on parts that already works, hopefully a [lexer] is still something
really simple. Nevertheless for the [f-lang] I wrote a lexer in a different way, I don't use a stack of states instead
I use a stream and when I am in a specific state I process it completely. The stream allow to consume characters from
everywhere. For example if the lexer found a digit character when starting a new token (just after a punctuation) it
will enter in a section that lex numeric literals, which can iterate throw the characters stream in the way it wants.
This approach let some parts of code reading in advance some characters without consuming them, so they can remind
available for next token. I don't really know how this design change impact the performances, but it should be close
from the previous one (maybe faster, maybe slower).

The [lexer] of [f-lang] is or will be a bit more complicated than the previous one I did few months ago because it have
a more generic purpose. The interpretation of string or numeric literals is more advanced, and actually I still haven't
added everything I want for this part of the language. I also think that the new design make the usage of hash tables
for punctuation useless (and may slow down the code), so I will probably remove them.

I changed the way of how I write lexers simply for comfort and maintainability of the code, this design seems to be the
most used, I saw it in the sources of many compilers. But I was surprised to listen [Sean Barrett] in a [discussion]
with [Jonathan Blow] whom seems to use a lexer with a design closer than the first one I made. [Sean Barrett] explains
greatly how this design allow more optimizations. For [f-lang] I am not so concerned by such advanced performances
tweaks, even more for the lexer. I can only recommend you to listen this great technical conversation about compilers
and speed.

Here is an example of an input code and the corresponding output:
```c
alias DWORD = ui32;
alias HANDLE = *void;

main :: (arguments : [] string) -> i32
{
    message:        string  = "Hello World";
    written_bytes:  DWORD   = 0;
    hstdOut:        HANDLE  = GetstdHandle(STD_OUTPUT_HANDLE);

    WriteFile(hstdOut, message.c_string, message.length, &bytes, 0);

    // ExitProcess(0);
    return 0;
}
```

![](/assets/images/F-lang-lexer_tokens.png)

The numbers at start of each lines are the line number and the column of fist character of the token.

With this result you can see almost all the data that I store in the Token structure, there is only few things like the
line number and type flags. All of that is straight forward, I didn't mention an obvious point that a token doesn't
have to store the referenced text as string. I simply use a view (pointer and size) on the file buffer that stay in
memory during the whole execution time of the compiler.

The next article should be about the parser, that works in a similar way with a stream, but its output is a bit more
complicated data structure.

[Crafting Interpreters]: https://craftinginterpreters.com/
[compiler definition]: https://en.wikipedia.org/wiki/Compiler
[f-lang]: https://github.com/Flamaros/f-lang
[lexer]: https://en.wikipedia.org/wiki/Lexical_analysis
[tokens]: https://en.wikipedia.org/wiki/Lexical_analysis#Token
[discussion]: https://www.youtube.com/watch?reload=9&v=rq1DRuB9p7w
[Sean Barrett]: https://nothings.org
[Jonathan Blow]: https://en.wikipedia.org/wiki/Jonathan_Blow
[AST]: https://en.wikipedia.org/wiki/Abstract_syntax_tree
