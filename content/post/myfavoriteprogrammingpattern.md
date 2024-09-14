+++
title = "My favorite programming pattern"
description = ""
tags = [
    "thoughts",
    "programming",
    "haskell",
]
date = "2022-01-18"
categories = [
    "thoughts",
]
+++

So at work we were talking about interviewer questions for graduates, and one of them was the standard simple test to weed out graduates who faked it till they made it. it went something like this:

> You are given a list of characters, please return the list with commas seperating each element. do not leave a trailing comma.

There may have been language restrictions or something, honestly I wasn't paying a huge amount of attention, but it got me thinking about my honest answer to that, and here is what I came up with.

In Haskell,

```haskell
join' :: [Char] -> ([Char] -> [Char])
join' (x:xs) = (x:) . dropLast . (join' xs)
   where
      dropLast = if (xs /= []) then (',':) else (id)
join' [] = id

answer :: [Char] -> [Char]
answer x = join' x $ []
```

For people who don’t know haskell, heres a quick run down of the important parts.

The function will look at the first character in the list, and partially apply it to the prepend function :

In haskell, you can partially apply functions, which means if you have a function taking two arguments, only give it one, and get back a new function that takes a single argument.

In the case of prepend, `x:` is a function that will take another list, and return that list prepended with `x`, ie

`'a' : ['b', 'c', 'd'] == "abcd" == ['a', 'b', 'c', 'd']`

We then compose that function with one of two functions returned by dropLast, dependending on whether `x` is the last character in the list.

Composing is done with `.`, and simply is a way to chain functions together. ie:

`(f . g)(x) == f(g(x))`

If the character we’re looking at isn’t the last, then we add another prepend for a comma. If it is the last, we add the `id` function into the mix, which just returns the input unchanged.

After that we recurse with the tail of the list.

At the end of this we aren’t given a comma seperated list like you’d think, we’re given a function. for the input "abcd" our function is something like:

`'a' : ',' : 'b' : ',' : 'c' : ',' : 'd' : id`

Which is a function that takes a list of characters, and returns a list of characters. to get our answer we need to give this function an argument, and the argument we want is an empty list.

The steps this function goes through build up the list from the back, prepending one character at a time, until we get our answer:

`"a,b,c,d"`

## Why I like this pattern

**It's not slow**

This one is more applicable to Haskell, but the naive way to do this (outside of using a prebuilt function) would be to use `++` and just go through each character adding it and a comma to a final output string.

Strings in Haskell are lists, and lists are linked lists under the hood. So appending anything requires traversing the entire list, and doing so in the way described above ends up being `O(n^2)`.

The prepend function method, however, is much faster. Prepending takes constant time, so doing so as shown only ends up being `O(n)` which is far far better.

**It uses `id`**

This is one of the few times I get to honestly use the `id` function for something other than teaching how lambda calculus works, so it gets points for that.

**It's fun and different**

You can't discount this, programming is a hobby and therefore should be fun. Not much more to say about that.

