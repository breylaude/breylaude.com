+++
title = "Static typing vs. Dynamic typing"
description = ""
tags = [
    "compilation",
    "c/c++",
]
date = "2018-08-03"
categories = [
    "compilation",
]
+++

Static typing. I suppose dynamic types are fine if you’re hacking together a small tool. But static (strong, preferably HM-inferred) typing allows you to reason about your program much more powerfully.

Correctness by construction, enforced by the compiler, is a good thing. I used to write a lot of unit tests; now I write many fewer, and instead I write my code closer to the ideal of *“if it compiles, it runs correctly”*.
