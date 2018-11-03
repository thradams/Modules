# Modules
Description for c compiler implementation.

Besides c compiler implementation, externals tools could be created to generate make files, visual studio solution, xcode solutions etc.. or existing tools could use the same representation as well as part of their configuration.

## What is a module?
A module is a set of files that can create a library. The module has an entry point that connects 
all source files (.c) related. 

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
Now we can create a new file separately from the orignal distribuition:

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


