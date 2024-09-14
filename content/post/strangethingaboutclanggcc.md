+++
title = "Strange thing about Clang/GCC"
description = ""
tags = [
    "gcc",
    "c++",
    "clang",
    "development",
]
date = "2018-06-05"
categories = [
    "c++",
    "development",
]
+++

Consider the following code:

```c
#include <iostream>
#include <type_traits>
 
// base template
template <typename T>
struct what_type
{
  void operator()()
  {
    cout << "T" << endl;
  }
};
 
// specialization 1
template <typename T, size_t N>
struct what_type<T[N]>
{
  void operator()()
  {
    cout << "T[" << N << "]" << endl;
  }
};
 
// specialization 2
template <>
struct what_type<int[0]>
{
  void operator()()
  {
    cout << "int[0]" << endl;
  }
};
int main(void)
{
  int x[] = {};
  what_type<std::remove_reference_t<decltype(x)>>()();
 
  return 0;
}
```
