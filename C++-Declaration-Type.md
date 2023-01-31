---
title: Declaration Type
description: 
published: true
date: 2023-01-31T16:05:14.169Z
tags: 
editor: markdown
dateCreated: 2023-01-31T15:04:04.072Z
---

If we would like to get a human readable interpretation of the declaration, we can use `cdecl`.  

```bash
while read -r line;
do
  echo '$line'|cdecl
done
```

###constants and pointers

- `const` and `int`(or others like `double`,`char` etc.) can be exchanged whenever they are put together(however, placing the const qualifier _last_ in the list of declaration-specifiers allows us to read the pointer modifiers in a declaration __backward__),hence the following are equal in syntax:  

  ```cpp
  const int * p;  // p is a pointer whose points-value is const
  int const * p;  //preferred
  //unlike below
  int * const pi; // pi points to a var and cannot points to another
  int const * const * pp;  //declare pp as a pointer to a const pointer to a const int
  const int * const * pp;
  ```
- type consistence and cast  

  ```cpp
  const int i1=40;
  const int *p1 = &i1; //ok
  int *p2 = &i1;  //compile error
  int *p3 = (int *) &i1;  //ok, however the i cannot be modified
  
  int i2 = 30;
  const int *q1 = &i2; //ok, but i2 cannot be changed through p1
  int *q2 = &i2;  //ok
  ```

### No `const` or `volatile` for arrays(migrate)

  ```c++
  typedef int A[12]; 
  extern const A ca; // array of 12 const ints
  typedef int *AP[12][12];
  volatile AP vm; // 2-D array of volatile pointers to int
  volatile int *vm2[12][12]; // 2-D array of pointers to volatile int
  ```

### `const` return type is only meaningful for _pointer/reference_("container")

[Should useless type qualifiers on return types be used, for clarity?](http://stackoverflow.com/questions/1579435/should-useless-type-qualifiers-on-return-types-be-used-for-clarity/1579459#1579459)

  ```c++
  const int foo();//system has no way to enforce that you not make changes to the returned int
  char * const foo2();//any code calling the function returning that type shouldn't modify contents of the string
  ```
