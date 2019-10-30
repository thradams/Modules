User-defined constants that do not interfere with the global namespace.

```c

const 'string' = 1;

enum HttpStatusCode 
{
   'OK' = 200,
   ...
};


int main()
{
  int i = 'string';
  enum HttpStatusCode  code = 'OK';
}


```
