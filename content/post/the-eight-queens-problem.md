+++
title = "The Eight Queens Problem"
description = ""
tags = [
    "backtracking",
]
date = "2019-11-06"
categories = [
    "cp",
]
+++

There are two difficulties: *1.)* How to judge whether a queen can be placed in a certain position; *2.)* Backtracking.

If you give this `8*8` matrix a coordinate, the horizontal (rows) is `i = 0` to 7, and the vertical (columns) is `j = 0` to 7. Then it can be found that in the direction of each diagonal line (/), the sum of the horizontal and vertical coordinates `(i + j)` of each grid is a fixed value, and the diagonal line from the upper left to the lower right has a value of `0~14` `(0+0; 0+1,1+0; 0+2,1+1,2+0; ...; 6+7,7+6; 7+7)`.

Similarly, in the direction of each backslash (), the relationship between the abscissa and ordinate of each grid is, `(i + (7-j))` is a fixed value, the diagonal line from top right to bottom left, Its value is `0~14` in sequence.

Therefore, before starting to find a solution, set an array for each of the horizontal, vertical, diagonal, and backslash directions. All elements are initialized to 0 (indicating that they can be placed). Then, check before placing the queen in `(i, j)`, whether `rows[i]`, `cols[j]`, `slash[i+j]`, and `bslash[i+7-j]` are all 0, it can be judged whether a queen can be placed in this position.

Since the placement process is placed line by line from top to bottom, these rows array can actually be removed.

In this way, the first difficulty is solved. *(Ps: bslash -> backslash)*.

The second difficulty, backtracking, I don’t think it's easy to make it clear. What needs to be backtracked is the aforementioned `colsslashbslash` array.

**Line by line**: each line is searched from the first position. When a valid position is found, a queen is placed, and the corresponding value is set to 1 (you can check whether it can be placed in a later position), and then continue to search for the next line position (*recursive call*); if there is no suitable position in the next line (*recursive return*), reset the corresponding position to 0, and then find the next position in this line.

This question is really not that tough. You can see that I'm free now.

As the saying goes:
> *Frequent bullshit can help prevent egg pain.*

```c
#include<stdio.h>
#include<stdlib.h>
#include <string.h>

#define N 8

int count;
introws[N], cols[N], slash[2 * N-1], bslash[2 * N-1];
//The position of each row, can’t be placed vertically, can’t be placed diagonally (/), diagonally Cannot put (\)

void place(int i) {
    int j;
    for (j = 0; j <N; ++j) {
        if(cols[j] == 0 && slash[i + j] == 0 && bslash[i + (N-1)-j] == 0) {
            rows[i] = j;

            cols[j] = 1;
            slash[i + j] = 1;
            bslash[i + (N-1)-j] = 1;

            if(i == N-1) {
                /*
                int k;
                for (k = 0; k <N; ++k) {
                    printf("%d ", rows[k]);
                }
                printf("\n");
                */
                count++;
            }
            else{
                place(i + 1);
            }

            //backtracking
            cols[j] = 0;
            slash[i + j] = 0;
            bslash[i + (N-1)-j] = 0;
        }
    }
}

int main () {

    memset(rows, 0,sizeof(rows));
    memset(cols, 0,sizeof(cols));
    memset(slash, 0,sizeof(slash));
    memset(bslash, 0,sizeof(bslash));

    count = 0;
    place(0);
    printf("count = %d\n", count);
    return0;
}
```
