# The perfect language between C and C++

## Member initializer

```cpp

struct X
{
   int i = 1;
   struct Point pt = { .x = 1, .y = 1 };
};

```

## Empty Initializer/Compound literal

```cpp
int main()
{
   struct X x = {}; //same as { . i = 1, .pt = {.x = 1, .y = 1 } }
   x = (struct X){}; //same as x = (struct X){ . i = 1, .pt = {.x = 1, .y = 1 } }
}


int main()
{
  //this will be unitialized just like C is today
  struct X x; 
}
```

C++ allows non constants in member initializers. I would prefer compile time constant only.


## Operator destroy

```cpp

struct Person
{
    char * name = NULL;
};

//overriding destroy
void operator destroy(struct X* p)
{
  free(p->name);
  
  //calling the  default destroy  
  default destroy(p); 
}

int main()
{
   struct X x;
   //...
   destroy(x);
}
```
## Calling destroy at the end of scope

Destroy is not called automatically at the end of scope unless you put 'auto'.

```cpp

struct Person
{
    char * name = NULL;
};

//overriding destroy
void operator destroy(struct X* p)
{
  free(p->name);
  
  //Just like init, destroy is defined for all types
  //we can call the default version of destroy
  default destroy(p); 
}

int main()
{
   //auto makes destroy to be called at the end of scope
   auto struct X x; 
   
} //destroy(x) is called at the end of scope
```

## Operator new

```cpp

struct X
{
    char * name = NULL;
};

int main()
{
  struct X* pX = new (struct X){ }; 
  
  //Same as:
  //struct X* pX = malloc(sizeof  * pX);
  //if (pX) *pX = (struct X){};
  
}

```


### Overriding new

```cpp

struct X
{
    char * name = NULL;
};

struct X* operator create(struct X* def)
{
  struct X* p = malloc(sizeof * p);
  if (p)
  {
    *p = *def;
  }
  return p;
}

int main()
{
  struct X* pX = new (struct X) {};  //same of create(&(struct X){});
}

```

## Operator delete

```cpp

struct X
{
    char * name = NULL;
};

int main()
{
  struct X* pX = new (struct X) {};
  
  delete pX;
  
  //same of
  
  if (pX)
  {
    destroy(*pX); 
    free(pX);
  }
  
}

```

### Overring delete

```cpp

struct X
{
    char * name = NULL;
};

void operator delete(struct X* p)
{   
   //...your code here
   default delete(p);
}

```

## Automatically calling delete

Use the auto modifier in your pointer.

```cpp

struct X
{
    char * name = NULL;
};

int main()
{
  struct X* auto pX = new (struct X) {};
   
} //delete pX is called at the end of scope

```

auto in struct members

```cpp

struct X
{
    char * auto name = NULL;
};

int main()
{
  struct X* auto pX = new (struct X) {};
   
} 
//delete pX is called at the end of scope
//delete will call the default destroy
//default destroy will call free in char*.
```



# Proposals for the C Language
Repository with proposals for the C language


## Standard build system 
Pragma source is a way to make source files discoverable respecting platform configuration.
This allow compilers and other tools like lint finds the source code in a standard way.
 
[Pragma source](pragmasource.md)

## Struct member initializers
[Struct Member initializer](memberinitialization.md)

## C Preprocessor Upgrade

[Preprocessor updgrade](prepocessorupgrade.md)

## User defined character constants

[User defined character constants](userdefinedchars.md)

## If with initializer and defer

[If with initializer and defer](ifdefer.md)
