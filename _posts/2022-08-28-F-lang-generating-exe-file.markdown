---
layout: post
title:  "F-lang: Generating exe file"
date:   2022-10-17 14:00:00 +0100
categories: C++
comments: true
---
## I am back
After more than two years I am back to write new posts. During this time a bought an house on which we did a lot of work and my girlfriend was pregnant.
Now I have a little boy that have almost one year. All of that took me a loot of my free time, but recently I finally was capable to work again on my project
[f-lang][16].

So since few months I develop again on my free time and I made a little project call [Dear Time][3] to learn [Dear ImGui][4] API and restore my little routine with
something really simple. And I finally restart the development of [my compiler][16]. I think that I now have a decent parser, and I started to work on a backend
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
the executable. I'll explain this in details soon.

### [Memory page][9]
A [Memory page][9] is the smallest amout of contiguous memory the CPU is capable to allocate, for x86 and x86_64 architectures this size is 4kB by default. Some CPU can be
configured by the OS to support larger sizes, but it is out of the scope of this post (follow the [Memory page][9] link for more details).  
A page can be configured by the OS depending on targetted usage, it can have rights and flags. For this post only understanding that there is rights for execution,
read and write are enough.  
You should already see it coming: *code* section require execution rights while *resources* section can be mapped on pages with read only flag.

### Understanding problems
[PE format][5] by it self isn't really complicated, it is just a sequence of data structures that have to be stored in a file like every file format. But the data it contains
is by nature difficult to store and restore in/from a file. To understand that just think to program that print the message "Hello World", in C/C++ it will look like this:
```cpp
#pragma comment(lib, "kernel32.lib")

#include <Windows.h>

int main()
{
    const char* message = "Hello World";
    DWORD written_bytes = 0;

    HANDLE hstdOut = GetStdHandle(STD_OUTPUT_HANDLE);

    WriteFile(hstdOut, message, 11, &written_bytes, 0);

    return 0;
}
```
The string "Hello World" will be placed in a section called *data* or *rdata* (for the read-only version). String literals are to big to be used directly by a CPU instruction
(some values can be immediately used as an instruction parameter, but we eventually see that in an other post), so we store it in a dedicated section. Now look at the "WriteFile"
function which take the variable message: it is a pointer variable. How the compiler will be able to know the address to put when generating the instruction responsible to call
"WriteFile" with this "message" parameter?  
The answer is: he simply can not predict this address, so the next logical question is, what does the compiler do then?  
It store in the executable some values necessary for the loader to know which values to patch and how to do it. Before going further in details, we will see the format.

### The format
Here is the structure for the 32 bits version of the [PE format][5], for the 64 bits version there is few differences but principles stay exactly the sames.
(This image comes from [Wikipedia][14])
<div style="text-align:center;width:125%;margin-left:-20%;margin-bottom:-12%;margin-top:-5%"><img src="/assets/images/Portable_Executable_32_bit_Structure_in_SVG_fixed.svg" /></div>
If you have paid enough attention to this picture, you should have noticed the mentions of *RVA* which is the acronym of Relative Virtual Adress. I'll explain what it is with an other
picture later. But for now we have to see some other properties:
+ **AddressOfEntryPoint**: *RVA* to the first instruction to execute. With this property the compiler is not forced to put the "main" at the begging of the *code* section.
+ **BaseOfCode**: *RVA* to the *code* section. It is necessary because the sections order isn't fixed and depending of the size of previous sections this value can change.
+ **BaseOfData**: Same as **BaseOfCode** but for *data* section.
+ **ImageBase**: The prefered address where to load the executable (all content of the file). The loader may failed to load it at this address especially for DLLs.
When the loader fails to load the image at this address it fix it in memory (don't forget [PE format][5] headers are loaded in memory too).
+ **SectionAlignment**: The alignment of sections when they are loaded into memory. Generally the [page][9] size of the targetted architecture is used.
+ **FileAlignment**: The alignment of section's data within the file. The value should be a power of 2 between 512 and 64KB and the default value is 512.

We already see that the entire file is loaded in memory, but with the two latest properties, *SectionAlignment* and *FileAlignment*, we are discovering that the mapping in memory is not direct.
For optimization issues the executable file use different alignment rules on hard drive and in memory. *FileAlignment* should be lesser or equal to *SectionAlignment* which means that the file
stored on hard drive is smaller or equal to the version loaded in memory. This is because even if we have much fewer RAM than space on hard drive, as we don't load all executables at the same time
we can waste some memory in favor of execution speed we gain when data of sections is aligned with [memory pages][9]. In contrary on the hard drive we want to optimize the occupied space, but we still
need to have a specific alignment to be able to load executables at a decent speed.

### RVA
I found the following picture on a [blog post][15] that illustrate differences between the layout of an executable as seen on an hard drive and into memory:
<div style="text-align:center;margin-bottom:20px"><img src="/assets/images/Portable_Executable_memory_mapping.png" /></div>

Now we have seen everything necessary to understand what is an *RVA*, it is an offset to which the loader will add the *ImageBase* value to obtain a memory address. Don't forget that the *ImageBase*
value can be changed by the loader if it was not able to load the executable at the prefered *ImageBase* address. Because it is related to memory this offset have to be computed as if the sections
were loaded into memory, that means by using *SectionAlignment* instead of *FileAlignment*.  
In the previous picture *BaseOfCode* value is 0x1000, because the memory address of *code* section (named ".text") is 0x01001000 and the *ImageBase* value is 0x01000000.  
And *BaseOfData* value is 0x9000, because the memory address of *data* section (named ".data") is 0x01009000.

Because I think that if you were able to understand the *RVA* principles with the differences between the executable representation on hard drive and into memory, every other computations related to
addressing issues should not be that hard to understand by yourself. So I will not cover relocation table and other things that can be found in the [PE format][5].  
But I just give you the following tip: don't use *IMAGE_DLLCHARACTERISTICS_DYNAMIC_BASE* if you don't want to bother you with relocation table. This flag is normally used only for dynamic libraries
(.dll files), but it works for executable too. Executables will never fail to be loaded to their prefered *ImageBase* address because the virtual memory system allow to give us the full memory of the system.  
Because I did reverse engineering on Visual Studio executables I had the flag *IMAGE_DLLCHARACTERISTICS_DYNAMIC_BASE* when I handcrafted my first executable, so I did more work than really necessary.  

Don't hesitate to ask me in comments if you want more details about anything, I'll try to answer you.  

## Useful tools
Here is a list of some tools I used to be able to reverse engineer the executables or find errors in my generated binary.
+ [ImHex][10]: An advanced hexadecimal editor with great features for reversing.
+ [PE-bear][11]: A [PE format][5] reversing tool.
+ [PE-file-checker][12]: A tool to check validity of an executable in [PE format][5].
+ [x64dbg][13]: An x86/x64 debugger made for reverse engineering of executables (debugging without sources).

It seems that there is some other tools that can be as good or maybe better than those I used, but once I was able to create a valid executable I stopped to try new ones.

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
[14]: https://en.wikipedia.org/wiki/Portable_Executable
[15]: https://unabated.tistory.com/entry/PEPortable-Executable-File-Format-1-PE-Header
[16]: https://github.com/Flamaros/[f-lang][16]

[jai]: https://inductive.no/jai/
