+++
title = "Difficulties with C++ Lambdas"
description = ""
tags = [
    "c++",
    "lambdas",
    "development",
]
date = "2018-06-02"
categories = [
    "c++",
    "development",
]
+++


C++ lambdas are wonderful for all sorts of reasons (especially with their C++14-and-beyond power). But I’ve run into a problem that I can’t think of a good way around yet.

If you are familiar with C++, you are aware of the importance of move semantics and rvalue references. Currently, a plethora of blog posts, conference videos, and even books explain how they operate, as well as how forwarding references (previously called universal references) combine with std::forward and templates to offer nice, optimal handling of objects with move semantics.

A further piece of the move-semantic completeness puzzle was added in C++14 with the introduction of generalized lambda capture, which allows lambda captures to be handled with move semantics.

The fact that reference qualifiers can be used in class member functions is another little-known but crucial detail of the overall picture. This is covered in section 9.3.1 [class.mfct.non-static] of the standard.

This implies that member functions can be overload with `&` or` &&` modifiers, and they will be called based on the value category of this, just like when we write member functions with const modifiers. Thus, you are aware of when it is safe to call `std::move` on data members.

Now, lambdas are conceptually like anonymous classes where the body is the `operator()` and the captured variables are the data members. And we can write lambdas with a mutable modifier indicating that the data members are mutable. (By far the common case is for them to be const, so the ordinary usage of const on a member function is inverted for lambdas.)

I said conceptually because it turns out they aren’t completely like that in at least one important way: lambdas can’t have reference qualifiers. Maybe for good reason – how would the compiler implement that? How would the programmer who wanted that behaviour specify the overloads he’s wanting? These are tricky questions to answer well.

But it is a problem – as far as I can tell so far, there is no way to know, inside a lambda, what the value category of the lambda object is. So the performance promise of the move semantic model falls down in the face of safety concerns: I don’t know whether I can safely move from a captured variable inside a lambda.

If anyone has any ideas about this, please let me know. Google and StackOverflow can tell me all about how move captures work with lambdas, but nothing about how to move things out of lambdas safely, or divine the value category of a lambda object.

All the things I’ve tried have either not worked, or have resulted in suboptimalities of various kinds. (And frankly, if anything had worked, at this point I’d put it down to a compiler quirk and not to be relied on.)

As far as I can tell, it’s a definite shortcoming of C++ that there’s no way to do this in a compile-time, type-inferred, lambda-land way.

I don’t see this in the core language issues – is it a known problem, or is there a known way to solve it that I don’t know yet?
