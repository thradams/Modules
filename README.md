# Modules
Description for compiler implementation


## What is a module?

```
5.1.1.1 Program structure
1 A C program need not all be translated at the same time. The text of the program is kept
in units called source files, (or preprocessing files) in this International Standard. A
source file together with all the headers and source files included via the preprocessing
directive #include is known as a preprocessing translation unit. After preprocessing, a
preprocessing translation unit is called a translation unit. Previously translated translation
units may be preserved individually or in libraries. The separate translation units of a
program communicate by (for example) calls to functions whose identifiers have external
linkage, manipulation of objects whose identifiers have external linkage, or manipulation
of data files. Translation units may be separately translated and then later linked to
produce an executable program.
```
A module is a **preprocessing translation unit** itself or a **preprocessing translation unit group** .

## How to group preprocessing translation unit?

The sugestion is to use #pragma source.

Let's say I want to create a module for console functions. 
Other projects can use this module.

My module has these files:

```
Console
  Src
     ConsoleWin.c
     ConsoleLinux.c
     UnitTest.c
  Include
     Console.h     
```

We want to especify that compiling for windows we need Console.h and ConsoleWin.c, for linux we need Console.h and ConsoleLinux.c  and UnitTest is not necessary.

Console.h
```c

#ifdef WIN32
#pragma source "..\Scr\ConsoleWin.c"
#elif LINUX
#pragma source "..\Scr\ConsoleLinux.c"
#endif

...

```

Now to use this:

MyProgram.c

```c
#include "Console.h"

int main()
{
}
```

To compile:

```
ccompiler -DLINUX MyProgram.c
```

Myprogram.c is a module group.

## References

comp lang c


