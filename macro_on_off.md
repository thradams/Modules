# Proposal to add pragma MACRO ON / OFF

## What is does?

Is turn ON OFF macro expansion.

## Motivation

Library implementers, especially in C++, uses identifiers names __LikeThis and one reason is to protect the code against some  macro created by the user.

In windows we have a classic conflict with min/max from windows.h and min max from STL.

With this feature can protect the code against external interference.


## How to use?

```c
#define M 1

#pragma STDC MACRO OFF

int main()
{
  int M = 2;
  
  #ifdef M   
   //... 
   int N;
  #else
   //...
  #endif
}

#pragma STDC MACRO ON

```
Result:
```c
int main()
{
  int M = 2;

  int N;
}
```

Generally we want to return the previous state. So this could be the default syntax:


```c
#define M 1

#pragma STDC MACRO PUSH OFF

int main()
{
  int M = 2;
  
  #ifdef M   
   //... 
   int N;
  #else
   //...
  #endif
}

#pragma STDC MACRO POP

```
