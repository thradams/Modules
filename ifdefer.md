# if with initializer

Just put C++ 17 if with initializer inside C.

Emulation with macros:

```cpp
#define _if(init, condition) for(init, *ptemp=(void*)1; ptemp && (condition); ptemp=0)
```

# if with initializer and defer 

Add and extra expression that is executed at the end of true branch of if.

Idiom
```cpp
  if (FILE* f = fopen("file.txt", "w"); f; fclose(f))
  {
  }
```

Emulation with macros:

````cpp
  #define __if(init, condition, defer) for(init, *ptemp=(void*)1; ptemp && (condition)  ; (defer), ptemp=0)
  
  __if(FILE* f = fopen("file.txt", "w"), f, fclose(f))
  {
    fprintf(f, "hi!");
  }
  
'''


# defer

This is the same of if with defer but the condition is always true

Idiom
```cpp
  defer(struct X x = {0}; X_Destroy(&x))
  {
  }
```

Emulation

```cpp

#define defer(init, defer) for(init, *ptemp=(void*)1; ptemp; (defer), ptemp=0)

struct X {
  int i;
};

void X_Destroy(struct X* p){}

int main()
{
  defer(struct X x = {0}, X_Destroy(&x)) {      
  }
}
  
'''

