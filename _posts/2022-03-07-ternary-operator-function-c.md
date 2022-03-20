---
layout: post
title: "Using the Ternary Operator in C to Choose Between Two Functions"
category: "Black Magic"
tags: C Programming
author: silas-bartha
---

I may have stumbled upon one of the strangest pieces of syntax in C.

I was recently writing a codec to convert a file between ASCII and hexadecimal formats. This was a school project written in C, and I was using the gcc compiler. I was using a command line flag to determine whether to encode or decode an input file, and had created the following enum to pass to the processing function along with the filename:

```c
    typedef enum {
        MODE_ENCODE,
        MODE_DECODE
    } Mode;
```

In my codec source file, I was thinking of a way to determine which of two functions to execute:

```c
    void encode(char* line);
    // or
    void decode(char* line);
```

These two functions are nearly the same, their return and parameter types are identical. It seemed like a shame to write out an entire if-else to choose between them when there were basically the same function:

```c
    void encode(Mode mode, char* filename) {
        //...
        if(mode == MODE_ENCODE) {
            encode(line);
        } else {
            decode(line);
        }
    }
```

Besides, I never really liked the way else blocks look visually. Call it strange but I tend to avoid them whenever possible. I'd much rather use a nice, compact ternary operator:

```c
    (mode ? decode(line) : encode(line));
```

Ah, much better. But what if I told you this could be simplified even further? Have a look at this:

```c
    (mode ? decode : encode)(line);
```

Beautiful. And it works, too! That's right, you can use a ternary operator to choose between two functions by name, and then call the function in the same line! If you've ever used function pointers you may remember assigning a function to a function pointer was as simple as using the function's name, and that's exactly what is happening in this situation. The ternary operator selects the proper function pointer based on the condition, and then we call it. Since these functions have identical *function signatures*, this technique works flawlessly.

I found this trick really fascinating, and I hope you did as well.

