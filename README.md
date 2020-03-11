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

## Operator init

```cpp
int main()
{
   struct X x;
   init(x); //same as :  x = (struct X){};
   
}

```
### Overriding init

Custom init

```cpp
struct X
{
   int i = 1;
   struct Point pt = { .x = 1, .y = 1 };
};

int init(struct X* p)
{
  //... your code
}

int main()
{
   struct X x;
   init(x); //calls your init
}

```
Calling the default init

```cpp

int init(struct X* p)
{
  //... your code
  default init(x); //calls the default init
}
```
## Operator destroy

```cpp

struct Person
{
    char * name = NULL;
};

//overriding destroy
void destroy(struct X* p)
{
  free(p->name);
  
  //Just like init, destroy is defined for all types
  //we can call the default version of destroy
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

Destroy is not called automatically.

```cpp

struct Person
{
    char * name = NULL;
};

//overriding destroy
void destroy(struct X* p)
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
