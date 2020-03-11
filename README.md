# The language between C and C++ I would like

Updated 11 march 2020

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

## Operator destroy

The compiler has a operator destroy (auto generated) that can be overrided.


```cpp
struct Person {
    char * name = NULL;
};

//overriding destroy for a variable of type struct X
void operator destroy(struct X auto x)
{
  free(x->name);
}

int main()
{
   struct X x;
   //...
   destroy(x);
}
```

## Calling the default implementation of destroy

```cpp

void operator destroy(struct X auto x) {
  free(x->name);
  default destroy(x);  //avoids infinite recursion
}

```

## Calling destroy at the end of scope

Destroy is not called automatically at the end of scope unless you qualify your variable as 'auto'.

```cpp

struct Person {
    char * name = NULL;
};

int main() {
   auto struct X x; 
   
} //destroy(x) is called

```

## Auto pointers

Pointers can be qualified with auto.

```cpp

struct X {
    char * name = NULL;
};

int main()
{
  struct X* auto pX = malloc(sizeof * pX);
  if (pX) *pX = (struct X){};
  
} //destroy(pX)

```

The compiler has a default implementation for any auto pointer that is equivalent of

```cpp
 if (p)
 {
   destroy(p);
   free(p);
 }
```

You can override destroy for some auto pointer type

```cpp
void operator destroy(struct X * auto p)
{
  if (p)
  {
    free(p->name);
    free(p);
  }
}
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

In case you don't want to use malloc for instance

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

### Overring destroy for auto pointers

In case you don't want to use free. (malloc/free pair changed)

```cpp

struct X
{
    char * name = NULL;
};

void operator destroy(struct X* auto p)
{   
   //...your code here      
}

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

## Custom operator
We can create other operators they are like funcions that can be overrided. But they have
internal linkage only.

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


## Polimorphism

Pointers that can point to a set of types. Types must have a common discriminant

Sample:
```cpp
 
 //forward declaration
 struct <struct Box | struct Circle> Shape;  
 
  //pShape points to box or circle
 struct Shape* pShape;
```

```cpp

struct Box
{
    int id = 1; //discriminant
};

void operator draw(struct Box* pBox) {
    printf("Box");
}

struct Circle {
    int id = 2; //discriminant
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
    draw(shapes[i]); //draw bust be an operator in box and circle othewise compile time error
    
    //id is the only data member
    printf("%d", shapes[i].id);
  }
  
}

```


## Standard build system 
Pragma source is a way to make source files discoverable respecting platform configuration.
This allow compilers and other tools like lint finds the source code in a standard way.
 
[Pragma source](pragmasource.md)




