+++
title = "Recursive lambdas"
description = ""
tags = [
    "c++",
    "lambdas",
    "recursion",
]
date = "2018-05-21"
categories = [
    "development",
    "c++",
]
+++

One can assign a lambda to auto or to `std::function`. Normally one would assign a lambda to auto to avoid possible unwanted allocation from `std::function`. But if you want recursion, you need to be able to refer to the lambda variable inside the lambda, and you can’t do that if it’s assigned to auto. So how do you do recursive lambdas without using `std::function`?

Use a fixed-point combinator (y-combinator) of course.

```cpp
#include <functional>
#include <iostream>
 
template<typename F>
struct Fix
{
  Fix(const F& f)
    : m_f(f)
  {}
 
  Fix(F&& f)
    : m_f(std::move(f))
  {}
 
  template <typename T>
  auto operator()(T t) const
  {
    return m_f(*this, t);
  }
 
  F m_f;
};
 
template<typename F>
auto fix(F&& f)
{
  return Fix<F>(std::forward<F>(f));
}
 
int main(void)
{
  auto f = fix(
      [] (auto& f, int x) -> int
      {
        if (x <= 2) return 1;
        return f(x-1) + f(x-2);
      });
  std::cout << f(6) << std::endl;
 
  return 0;
}
```
This code compiles with `GCC 4.9.1`, `Clang 3.4.2` or `MSVC 2015` preview (and produces *“8”* as the output). Clang allows omission of the lambda’s trailing return type.


