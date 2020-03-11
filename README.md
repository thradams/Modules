# The language between C and C++ I would like

Updated 11 march 2020

When reading this document consider that the features are additions into C11 and not changes in C++.
C++ features are not existent here by default (e.g references)

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
   struct X x = {}; 
   
   //same as C99
   struct x = { . i = 1, .pt = {.x = 1, .y = 1 } };
   
   x = (struct X){}; 
   
   //same as C99
   x = (struct X){ . i = 1, .pt = {.x = 1, .y = 1 } };
}


int main()
{
  struct X x; //same as C today (unitialized)
  
  int i = {};  //same as i = 0;
  int* p = {};  //same as p = 0;
  float f = {}; //same as f = 0.0f
  //etc..
}
```

## Operator destroy

Does nothing for ints, structs etc..
Can be overrided to do something.

```cpp

struct Person
{
    char * name = NULL;
};

//overriding destroy
void operator destroy(struct X* p)
{
  free(p->name);
  
  //calling the  default destroy   (does nothing in this case)
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

int main()
{
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
  
  struct X* pX = malloc(sizeof  * pX);
  if (pX) 
  {
    *pX = (struct X){};
  }  
}

```

### Overriding new

```cpp

struct X
{
    char * name = NULL;
};

struct X* operator new(struct X* def)
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
  struct X* pX = new (struct X) {};
}

```

## Auto pointers

```cpp

struct X
{
    char * name = NULL;
};

int main()
{
  struct X* auto pX = new (struct X) {};
  
  destroy(pX);
  
  //Same of
  
  if (pX)
  {
    destroy(*pX); 
    free(pX);
  }  
}

```

### Overring destroy for auto pointers

```cpp

struct X
{
    char * name = NULL;
};

void operator destroy(struct X* auto p)
{   
   //...your code here
   
   default destroy(p);
}

```

The default destroy for auto pointer is the same of

```cpp

if (pX)
{
  destroy(*pX);
  free(pX);
}

```

## Automatically calling destroy for auto pointers

Use the auto modifier in your pointer.

```cpp

struct X
{
    char * name = NULL;
};

int main()
{
  struct X* auto pX = new (struct X) {};
   
} //destroy(pX) is called at the end of scope

```

## Auto in struct members

auto in struct members will generate the default destructor
calling destroy for each


```cpp

struct Y
{
  int i;
};

struct X
{ 
  auto struct Y y;
  struct Y * auto pY;
};

int main()
{
  auto struct X x = {};  
} 
calls destroy(x)
that will call destroy(x.y) and destroy(x.pY) that will call destroy(*pX) and free(pX)


````
## if with initializer 
Same of C++.  See the use of auto.

```cpp

struct Y
{
  int i;
};

struct X
{ 
  auto struct Y y;
  struct Y * auto pY;
};

int main()
{
  if (struct X* auto pX = new (struct X){}, p)
  {
  }//calls destroy(pX);
} 

````
## overriding destroy for existing types

```cpp

void operator destroy (FILE * auto f)
{
  fclose(f);
}

int main()
{
  if (FILE* auto f = fopen("file.txt", "r"), f)
  {
  }//calls custon destroy(f) that calls fclose;
} 

````
# Operator move

```cpp
 struct X * auto pX1 = new (struct X){};
 struct X * auto pX2 = {};
 
 pX2 = move(pX1);

//Same as:
pX2 = pX1;
pX1 = NULL;

```

## Lambdas 
Similar of C++.

Lambdas without capture will be function pointers.
Lambdas with capture (to be defined)


## Standard build system 
Pragma source is a way to make source files discoverable respecting platform configuration.
This allow compilers and other tools like lint finds the source code in a standard way.
 
[Pragma source](pragmasource.md)




