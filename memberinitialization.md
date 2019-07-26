# Struct member initializers


Read about the C++ proposal here:

http://www.open-std.org/JTC1/SC22/WG21/docs/papers/2008/n2756.htm

I consider the motivation for C is a little diferent and my sugestion is
to allow **only constant expression** for initialization. 

C++ allows everthing see this sample in C++
```cpp
intTest on Compiler Explorer ! ret = 0;
int main () {
    struct {
        int x = ++ret;
    } x = {0};
    return ret;
}
```
See in https://cor3ntin.github.io/posts/auto_nsdmi/

I think the member initializer should be used to define default values
at compiler time together with the type.




## What I do today in C

```c
struct Y
{
  int i;
}

#define INIT_Y {.i = 1}

struct X
{
  struct Y y;
  int i2;
}

#define INIT_X {INIT_Y, .i2 = 2}


struct X x = X_INIT;
```

With this sugestion we can remove macros and extra definitions:

```c
struct Y
{
  int i = 1;
}
struct X
{
  struct Y y;
  int i2 = 2;
}
struct X x = {};
```

