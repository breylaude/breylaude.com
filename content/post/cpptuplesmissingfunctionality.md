+++
title = "C++ Tuples missing functionality"
description = ""
tags = [
    "c++",
    "development",
]
date = "2018-04-16"
categories = [
    "c++",
    "development",
]
+++


C++ provides a strange mix of compile-time and runtime functionality for dealing with tuples. There are some interesting parts, like `std::tie` to destructure a tuple, and `std::tuple_cat` to join together several tuples into one.

So there is evidence that the standard has been influenced by some functional programming ideas, but I don’t think the full power of tuples has been realized (in both senses), and I found myself thinking about some missing parts.

Like why do we have `std::tuple_cat` and `std::get<0>` (aka `std::tuple_head`), but not `std::tuple_cons` or `std::tuple_tail`? So here are some possible implementations.

First, something missing from `type_traits` which will help us constrain things:

```c
template <typename T>
struct is_tuple : public std::false_type {};
template <typename... Ts>
struct is_tuple<std::tuple<Ts...>> : public std::true_type {};
Both tuple_cons and tuple_tail can make use of an expansion of std::index_sequence to work their magic, with a user-facing function that provides the appropriate std::index_sequence to an overload.

For tuple_cons, we just call std::make_tuple with the new element and the result of expanding std::get across the other tuple:

template <typename U, typename T, std::size_t ...Is>
auto tuple_cons(U&& u, T&& t, std::index_sequence<Is...>,
    std::enable_if_t<is_tuple<std::decay_t<T>>::value>* = nullptr)
{
  return std::make_tuple(std::forward<U>(u), 
                         std::get<Is>(std::forward<T>(t))...);
}
 
template <typename U, typename T>
auto tuple_cons(U&& u, T&& t,
    std::enable_if_t<is_tuple<std::decay_t<T>>::value>* = nullptr)
{
  using Tuple = std::decay_t<T>;
  return tuple_cons(std::forward<U>(u), std::forward<T>(t),
      std::make_index_sequence<std::tuple_size<Tuple>::value>());
}
And for tuple_tail, we construct a std::index_sequence of length n-1, then offset it by one in the expansion to obtain the tail:

template <typename T, std::size_t ...Is>
auto tuple_tail(T&& t, std::index_sequence<Is...>,
    std::enable_if_t<is_tuple<std::decay_t<T>>::value>* = nullptr)
{
  return std::make_tuple(std::get<Is + 1>(std::forward<T>(t))...);
}
 
template <typename T>
auto tuple_tail(T&& t, 
    std::enable_if_t<is_tuple<std::decay_t<T>>::value>* = nullptr)
{
  using Tuple = std::decay_t<T>;
  return tuple_tail(std::forward<T>(t),
      std::make_index_sequence<std::tuple_size<Tuple>::value - 1>());
}
```
