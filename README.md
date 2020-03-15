# The language between C and C++ I would like to have

Updated 12 march 2020

When reading this document consider that the features are additions into C and not changes in C++.

## Member initializer

```cpp

struct X {
   int i = 1;
   struct Point pt = { .x = 1, .y = 1 };
};

```

## Empty Initializer/Compound literal

```cpp
int main() {
   
   //new syntax
   struct X x = {};    
   //same as C99
   struct X x = { . i = 1, .pt = {.x = 1, .y = 1 } };
   
   //new syntax
   x = (struct X){};    
   //same as C99
   x = (struct X){ . i = 1, .pt = {.x = 1, .y = 1 } };
}


int main() {
  struct X x; //same as C today (unitialized)
}
```
The initialization is static. (!= from C++)

### Operator new

The compiler auto generates the implementation of the new operator.

The usage (syntax):

```cpp

struct X {
    char * name = NULL;
};

int main() {
  struct X* pX = new (struct X) {};
}

```

The default implementation is :

```cpp
struct T* operator new(struct T initValue)
{
  struct T* p = malloc(sizeof initValue);
  if (p)
  {
    *p = initValue;
  }
  return p;
}
```

It does not throw. There is no runtime (appart of malloc) error.


We can override this operator. For instance to use a custom malloc or just
to intercept the call and do some other operation.
The default implementation can be explicitly called usind

```cpp
struct T* p = default new(initValue);
```

```cpp
struct T* operator new(struct T initValue)
{
  struct T* p = malloc(sizeof * p);
  if (p)
  {
    *p = initValue;
  }
  return p;
}
```

New operator works for arrays: (Any compound literal)

```cpp

int main() {
  char * str = new((char[200]){});
}
```

## Operator destroy

The compiler has an auto generated operator destroy that can be overrided
for some especific type.


```cpp
struct Person {
    char * name = NULL;
};

//overriding destroy for a variable of type struct X auto
void operator destroy(struct X x) {
  free(x.name);
}

int main() {

   struct X x;
   //...
   destroy(x);
}
```

In the same way of new, the default destroy will call the default implementation
that does nothing for basic types.  The useful generation of destroy is associated
with the keyword auto that will see in the next topics.

## Calling destroy at the end of scope

Destroy is not called automatically at the end of scope unless you qualify your variable as 'auto'.

```cpp
int main() {
   auto struct X x; 
   
} //destroy(x) is called

```

## Auto pointers

Pointers can be qualified with auto, it means that the pointer is the onwer of the pointed object.

```cpp

struct X {
    char * name = NULL;
};

int main()
{
  struct X* auto pX = new (struct X){};
  
} //destroy(pX) is called

```

The compiler has a default implementation for any pointer qualified with auto that is equivalent of

```cpp
void operator destroy(T * auto p)
{
  if (p)
  {
    destroy(*p);
    free(p);
  }
}
```

To destroy the content of a non auto pointer we can cast.

```cpp
  struct X* pX = new (struct X){};
  ...
  destroy( (struct X* auto) pX);  
```

Or call destroy for the content and them free.

```cpp
  struct X* pX = new (struct X){};
  ...
  destroy(*pX);  
  free(pX);
```

## Auto in struct members

The default implementation of destroy calls each member recursivally.

So

```cpp

struct Y {
  int i;
};

struct X
{ 
  auto struct Y y;
  struct Y * auto pY;
};

int main() {
  auto struct X x = {};  
} 
````
destroy(x) is called at the end of scope. 

The default implementation will call destroy(x.y) and destroy(x.pY);


## if with initializer 
Same of C++.  Togueter with auto it creates an interting pattern.

```cpp

  if (struct X* auto pX = new (struct X){}, p)
  {
    //pX in scope AND != NULL
    ..
  }
  //pX not in scope

````

## overriding destroy for existing types

typedefs are only alias in C. But for the operators they are considered.
For instance, let's say FILE is a typedef for int. We can override
the operator and that doen't means that any int will call this function.

```cpp

void operator destroy (FILE * auto f)
{
  fclose(f);
}

int main()
{
  if (FILE* auto f = fopen("file.txt", "r"), f)
  {
  }
} 

````
# Operator move (source)
Copies source to dest and clear source.

```cpp
 struct X * auto pX1 = new (struct X){};
 struct X * auto pX2 = {};
 
 pX2 = move(pX1);

 //Same as:
 pX2 = pX1;
 pX1 = NULL;

```
# Operator swap (a, b)

Copy a to b and b to a.

```cpp
  
  T t = a;
  a = b;
  b = t
  
```

## Lambdas 
Similar of C++.

Lambdas without capture will be function pointers.
Lambdas with capture (to be defined)

## Custom operator

We can create other operators that basically are functions that can be overriden.
They are not normal functions because they have name mangling (like c++) or internal
linkage (like static inline functions)

They are useful do  create some operation that is applied for many types with the same 
meaning.

```cpp

struct Box {
    int id = 1;
};

void operator draw(struct Box* pBox) {
    printf("Box");
}

struct Circle {
    int id = 2;
};

void operator draw(struct Circle* pCircle) {
    printf("Circle");
}

int main()
{
  struct Box box = {};
  struct Circle circle = {};
  
  draw(&box);
  draw(&cicle);
}

```
This can be used in math operations for instance.

## Generic Operators

Considered.

## Polimorphism

Pointers that can point to a especific set of types.

If you set of types have a common discriminant we also can select the
appropriated operator in runtime according with the type.

Sample:
```cpp


 
struct Box
{
    const int id = 1; //discriminant
};

void operator draw(struct Box* pBox) {
    printf("Box");
}

struct Circle {
    const int id = 2; //discriminant
};

void operator draw(struct Circle* pCircle) {
    printf("Circle");
}

int main()
{
  auto struct <Box | Circle> * auto shapes[2] = {};
  
  shapes[0] = new (struct Box){};
  shapes[1] = new (struct Circle){};
  
  for (int i = 0; i < 2; i++)
  {    
    //runtime selecion according with the discriminant
    draw(shapes[i]); 
    //draw must be an operator in box and circle othewise error
    
    //when types have a common discriminant it is available
    printf("%d", shapes[i].id);
  }
  
}

```

## Resizable arrays

```cpp

 void operator push(auto a[auto], auto item)
 {
    if (a.size + 1 > a.capacity)
    {
        int n = a.capacity * 2;
        if (n == 0)
        {
            n = 1;
        }
        decltype(a.data[0]) * pnew = a.data;
        pnew = realloc(pnew, n * sizeof(a[0]));
        if (pnew)
        {
            pItems->data = pnew;
            pItems->capacity = n;
        }
    }
    a.data[a.size] = item;
    a.size++;
 }
 
 int a[auto]; //synonym of struct some_name_mangling  { int * data; int size; int capacity;}
 push(a, 1);
 
 a.data[0] = 1; //ok
 a[0] = 1; //ok
 
```

## Standard build system 
Pragma source is a way to make source files discoverable respecting platform configuration.
This allow compilers and other tools like lint finds the source code in a standard way.
 
[Pragma source](pragmasource.md)




