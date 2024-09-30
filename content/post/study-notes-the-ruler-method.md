+++
title = "[Study Notes] The ruler method maintains information that does not support deletion"
description = ""
tags = [
    "algorithm",
]
date = "2019-09-13"
categories = [
    "dp",
]
+++

I took this trick in the mock competition in the morning, and then I didn't know it, so I decided to post.

Consider a problem of finding the shortest interval, which satisfies monotonicity (two-pointers can be used), insertion is fast, but deletion is very slow, it is possible to quickly judge whether the combination of two pieces of information can meet the conditions.

At this time, the ordinary ruler method cannot be used.

### How to solve it?

(Assuming inserts and merges are `O(1)O(1)`)

- Algorithm 1:

Consider computing the answer through the center, then divide and conquer.

Specifically, the interval information of [i, mid] and [mid+1, r] is calculated. Since the monotonicity is satisfied, the answer can be calculated in two-pointers.

Time complexity `O(nlogn)`

However, there are some problems

- Algorithm 2:
Consider using two stacks to maintain information, the first stack maintains the information of each suffix in the current `[l,mid]` interval (the left endpoints are `l, l + 1,……,mid`), the second stack maintains the information of `[mid + 1,r]` (doesn't actually need a stack?).

Then each time `++ra` new element is pushed into the second stack, and `++lone` is popped from the first stack. If the first stack is empty, put the entire second stack into the first stack.

Done.

Time complexity `O(n)O(n)`.

Small example `ωω` cactus

Small `ωω` There are `s` items, and each item has a certain size and weight.

She can go from any `L-th` item to the `R-th` item, and the items in this range can be selected or not.

The size sum of the items she takes out must be `w`, and the weight sum must be `<= k`.

She wanted to know what the shortest interval was.

You tell her `s ≤ 10000,w ≤ 5000s ≤ 10000`, `w ≤ 5000`.

- Practice:
Maintenance backpack. The merge judgment is to enumerate the size of the left side x and the right side wx.

Time complexity `O(sw)`
