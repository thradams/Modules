# Proposal to add MACRO_ON / MACRO_OFF

## What is does?

Is turn ON OFF macro expansion.

## Motivation

Library implementers, especially in C++, uses identifiers names __LikeThis and one reason is to protect the code against some  macro created by the user.

In windows we have a classic conflict with min/max from windows.h and min max from STL.

With this feature can protect the code against external interference.


## How to use?

```c
#pragma STDC MACRO_OFF

  the preprocessor will not expand any macro here...
  
#pragma STDC MACRO_ON

```

Other preprocessor features are not affected and the expansion 
at for instance #if HERE still allowed. 

