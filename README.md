# Modules
Description for c compiler implementation.

Besides c compiler implementation, externals tools could be created to generate make files, visual studio solution, xcode solutions etc.. or existing tools could use the same representation as well as part of their configuration.

## What is a module?

From the C standard;

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
The concept I want to add is module.

A module is a group of source codes related. For instance, the group of source code necessary to create one traditional library.

A module has a root file, and we can find all other related files using this root.


## How to create this root file and how files are related?

The sugestion is to use **#pragma source** to create link beetween files.

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

At Console.h we add **#pragma source**

```c

/*
   Console.h
*/

#ifdef WIN32
#pragma source "..\Scr\ConsoleWin.c"
#elif LINUX
#pragma source "..\Scr\ConsoleLinux.c"
#endif

...

```

To use this module **all we need to do is to include the Console.h**

```c
/*
  MyProgram.c
*/

#include "Console.h"

int main()
{
}
```

To compile:

```
ccompiler -DLINUX MyProgram.c
```


## Non intrusive

Let's say the module for console was created before the module concept.

```
Console
  Src
     ConsoleWin.c
     ConsoleLinux.c
     UnitTest.c
  Include
     Console.h     
```
Now we can create a new file separately:

ConsoleModule.h

```c
/*
   ConsoleModule.h
*/

#include "Console.h"

#ifdef WIN32
#pragma source ".\Console\Scr\ConsoleWin.c"
#elif LINUX
#pragma source ".\Console\Scr\ConsoleLinux.c"
#endif
```

I can now use this module in MyProgram:

```c
/*
  MyProgram.c
*/

#include "ConsoleModule.h"  
int main()
{
}
```

```
ccompiler --DWIN32 MyProgram.c
```

Imagine module descriptions for all existing libraries: openssl, libtiff, zlib, libjpeg..
Then if you want to use some of these libraries in your project you can just copy a folder and include some file.

We also can make the entire MyProgram a module in a way that the original source files don´t need the pragma source.

MyProgram.c
```c
#include "Console.h"  
int main()
{
}
```

MyProgramModule.c
```c

/*
  MyProgramModule.c
*/

#include "ConsoleModule.h"
#pragma source "MyProgram.c"

```

```
ccompiler --DWIN32 MyProgramModule.c
```


## Implementation

Basically at preprocessor phase we collect and merge the pragma sources considering their full paths. 
Then this map is compiled in any order and some flags can mark the file as alredy compiled.

## Automatic file deduction?

If some c file has the same name of header at the same position should we consider that this file is the pragma source?

```
   File1.h
   File.c
```

Then File1.h dont need to say pragma source "File1.c"
Whould we search in other directories?

## Other pragmas

The idea is give information about libraries and include dir.

```c
#pragma includedir ""

#pragma library ""
//see #pragma comment(lib, "lib.lib") from Microsoft compiler

```

## References

comp lang c


