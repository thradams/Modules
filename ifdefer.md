# if with initializer

Just copy C++ version:


# if with initializer and defer

Add and extra expression that is executed at the end of true branch of if.

Idiom
```cpp
  if (FILE* f = fopen("file.txt", "w"), f; fclose(f))
  {
  }
```

# defer

This is the same of if with defer but the condition is always true

Idiom
```cpp
  defer(struct X x = {0}; X_Destroy(&x))
  {
  }
```
