+++
title = "Realizing the Difficulty of a Case only after running a Performance Test"
description = ""
tags = [
    "notes",
]
date = "2023-04-10"
categories = [
    "",
]
+++

No matter how thorough the preparations are, there are quite a few cases where the case can't be executed as expected when it is actually executed. My experience is that the first execution fails in 90% of cases.

Then, because the execution cost is high, re-execution is difficult in terms of scheduling.

The solution to this problem is to prepare a trial case and run it immediately.

The essence of the problem is that “I didn’t really know what to prepare for that performance test case” after all, and that the preparation itself was not enough in the preparation before execution.

Then the story is simple, just run the case once. However, if you try to execute a really necessary case, it will cost a lot, such as preparing a large amount of data, so the problem has not been solved.

Therefore, it is sufficient to execute one case with the execution cost extremely low by making the amount of data considerably smaller than it should be. I call this the trial case.

The purpose of this trial case is to determine the necessary elements for executing performance tests, so it is not necessary to complete all preparations for log and metrics acquisition. It is better to have it, but the priority should be to prepare the environment so that the case can be executed.
