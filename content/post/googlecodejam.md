+++
title = "Google Code Jam 2018"
description = ""
tags = [
    "cp",
    "c++",
]
date = "2018-04-27"
categories = [
    "cp",
    "solutions",
]
+++

Codejam is using a different system and rules than before. I'm not sure if I want to participate in the contest. I know how to solve problem A , but I can't find a vital bit of info about the new rules like the CPU of the servers where the codes are supposed to run.

However, this suspiciously sound like one of those badly made ACM contests.

I no longer have so much free time as before. I barely have time in the weekends to rest. So a Saturday is not something I can just spend in some contest if it doesn't sound at least a bit challenging. And the prizes are as low as ever. I decide that atleast for Friday I'm not gonna bother.

Enough talking. I accidentally read problem B and figure it's an extremely easy problem.

## Problem B

There is a badly made sorting algorithm. Taking an array `V`, it finds two `indexes i`, and `(i+2)` such that `V[i+2]>V[i]`.

In that case, it takes the part of the array: `(V[i]`,`V[i+1]`,`V[i+2])` and reverses it `(V[i+2]`,`V[i+1]`,`V[i])`.

It repeats this until it can’t find any more such `i`, and `(i+2)` pairs.

This algorithm can’t always sort the array correctly, and given an input `V` supposed to predict the part where the algorithm is going to fail. If it fails, we have to find the first index that is not sorted correctly. For B large the number of elements can be up to `100000`.

The algorithm is `0(n2)` so for the large version of the problem I can't just simulate it.

The key of the problem is to notice that after reversing `(V[i]`,`V[i+1]`,`V[i+2])` into `(V[i+2]`,`V[i+1]`,`V[i])`, the position of `V[i+1]` is not going to change. So the operation is the same as swapping `V[i]` and `V[i+2]`, which are the elements compared. This means that there are actually two independent partitions of the array.

The elements with even indexes will never interact with the elements with odd indexes. So imagine the array [6,5,4,3,2,1], it has two independent sub-arrays: `[6,..,4,..,2,..]` and `[5,..,3,..,1]` and in these sub problems, all you can do is swap consecutive elements.

This means that the two sub arrays are being sorted with normal bubble sort. And eventually the bubble sorts will end running and I will get `[2,..,4,..,6,..]` and `[1,..,3,..,5]` and when I put the two sub-arrays back into the big array I get `[2,1,4,3,6,5]` and it is clearly not sorted.

*To solve this problem*, just split the array into two sub-arrays, one with the even indexes and one with the odd indexes. Sort the two arrays and put them back together. If the result is sorted, then all is fine, else returning the first index where it breaks.

```
// get the sub array with parity (0 or 1) vector<int> getv(const vector<int> &V, int parity) { vector<int> r; for (int i = parity; i < V.size(); i += 2) { r.push_back( V[i] ); } return r; }

// mix two sub-arrays back into one large one: vector mix(const vector &v1, const vector &v2) { vector V; for (int i = 0; i < v1.size() + v2.size(); i++) { V.push_back( (i%2 == 0)? v1[i/2] : v2[i/2] ); } return V; }
```

```
string solve(const vector<int> &V) { // split vector<int> v1 = getv(V,0); vector<int> v2 = getv(V,1); // sort sort(v1.begin(), v1.end()); sort(v2.begin(), v2.end()); // merge again vector<int> v3 = mix(v1,v2); // check for (int i = 0; i+1 < V.size(); i++) { if (v3[i] > v3[i+1]) { return to_string(i); } } return "OK"; }
```

I changed my mind, apparently. I'm too bored to bother with **A**, I opened **C**. Problem C is an interactive problem.

## Problem C
You are given a `1000 x 1000` grid and want to paint at least `A` cells (in the easy version, `A` is `20` and in the hard version, `200`).

## Objective
The bounding box of all the painted cells should not contain any unpainted cell. And the catch is that when you pick a single cell to paint, the system is actually going to pick one of the `9` squares in the neighborhood of the cell you picked and paint that one.

Even if the cell is already painted, if the system picks it, it will paint it again and waste a turn. You have only `1000` turns.

Of course it picks any of the `9` squares in the neighborhood with `1/9` probability.

Seems interesting enough that I would have been able to have in the old system (or maybe I could, by making the system expose a public API that interacts with your program?).

Atleast interesting enough. The solution is to come up with a strategy and prove that the probability that it needs more than `1000` turns is very low.

## Lame solution
Turning it into a linear problem. Instead of worrying about 2D rectangles, you go in a straight line from bottom to top.

Imagine you keep giving the system the order to paint at point `(x,y)`. Eventually, all `9` points around it will be painted. Once this happens, you can start returning `(x, y+3)` and paint another `3x3` square.

And keep repeating. Until you’ve painted enough squares. The result will always be a perfect rectangle.

The only catch is to calculate that this will tend to require less than `1000` steps. Actually, due to the random nature of the problem, there is always a probability that for some reason it could pick the same point `1000` times, but that is a very improbable thing.

## Calculation
The expected value for the number of turns needed before filling a complete `3 x 3` square. The number is between `25` and `26`. To paint at least `20` squares, I would need `3` squares of `3x3` in total.

That means that the expected number of turns I’ll need is `228`, which is pretty reasonable.

Of course, the expected value is not the same as the probability it will work in less than `1000` steps. But a) It is easier to calculate and b) The expected value is designed to give a good estimate.

It’s the average number of turns I will need, and because everything is random, it would be a bit insane to expect that the number of turns will get much larger than `228`. Definitely not up until `1000`…

## Better solution
There’s no need to wait for the whole `3 x 3` square, I only need to wait for the bottom row of `3` square cells. Once it's full, I can start trying a square higher.

Basically, starting with `(x,y)`, once the row of `3` cells bellow `(x,y)` is full, start trying with `(x, y+1)` and so on and so on. I only need to figure out a good ending condition. If filling the current `3 x 3` square surrounding the current `(x,y)` is enough to complete the requirement for `A`, then I can stop…

It showed that the expected number of turns will be around `106` for `A=20` and more than `1200` for `A=200`. Means it would pass the easy but not the hard problem. But in fact my calculations where over pessimistic and it passes the hard version of the problem as well.

*You may be wondering how the hell did I calculate all of this?* Well, here is an example: Imagine that you will keep printing `(x,y)` until the bottom row of `(x−1,y−1)`, `(x,y−1)` and `(x+1,y−1)` are all painted. What is the expected number of steps we need to perform?

There are `9` cells in total that can be painted by `(x,y)` move but our objective is to paint `3` of them. The function 𝑓(𝑡) will tell the expected number of turns before painting t specific cells (the other ones don’t matter.

For `f(0)` There are `0` cells that matter to us, so I don’t need to paint anything, all is ready.

For `f(1)` I need at least one turn. There are two possibilities: With probability `19`, I painted the correct cell and there are now zero more cells to paint. The expected number of steps will be `1+f(0)`.

Then I can use that result to calculate `f(2)` (it’s the same logic), and so on and so on. By filling the values of `f(t)`, I could find that it takes around `16` turns before filling the bottom row and *25.something* steps before filling the whole square.

With probability `89`, I painted a cell that doesn’t matter so there is still one special cell. The expected number of steps will be `1+f(1)`.

The total is: `f(1)=(19)(1+f(0))+(89)(1+f(1))`, note the recursion, but the recursion is not a problem, just consider `f(1)` as a variable, like z: Solve the equation `z=(19)(1+f(0))+(89)(1+z)`, the result of `z` will give `f(1)`.

The reason this estimate is too pessimistic is that I don’t consider that after filling the bottom row, there will be many squares in the `(x,y)` neighborhood that are painted, which increases the chance that future iterations of `y` are going to receive less work to do.

Turns out the problem came with a tester tool to interact with your program locally. But I didn’t notice until after solving the whole problem and testing manually multiple times.



