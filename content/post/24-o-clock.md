+++
title = "24 o'clock"
description = "This article contains a very bad algorithm and a very good algorithm"
tags = [
    "backtracking",
]
date = "2020-06-15"
categories = [
    "cp",
]
+++

24 points is a sufficiently old game: give four one-digit (may be repeated), use addition, subtraction, multiplication, division and parentheses to integrate these four numbers into one formula, making the result of the formula equal to 24. Of course, not all four single digits can be combined into 24, such as 1,1,1,1 obviously cannot. Two classic combinations are 1, 5, 5, 5 and 3, 3, 8, 8-can you find a valid solution?

For any given four one-digit numbers, if you do not consider the repetition (such as 1, 5, 5, 5, three 5 as different numbers) then it can be calculated, the possible formulas are (432 1) * (12+11+21) * (4 * 4 * 4) = 7680 kinds. It is very unreasonable to use just a human brain to treat such a large number, so a program should be written to deal with it, eh. (What is 1 * 2 + 1 * 1 + 2 * 1 in the middle?- I’ll talk about it later...) I thought about the realization of this program a year ago, but at that time I had a poor grasp of the language, let alone write it specific backtracking and other algorithms are implemented. Yesterday, I thought about this problem again, after so long, I finally wrote the code today (one question for years, so depressed when I ran).

I thought of an idea yesterday.

An equation can be regarded as a tree. The characteristics of this tree are: the branch node contains an operator to perform the corresponding operation, it must include the left subtree and the right subtree; the leaf node is the operand, it has no subtree. The calculation of this formula starts from the root node, recursively calculates the values ​​of the left and right subtrees, and then calculates the results of the two according to the operator of the root node, and the result is the value of the formula.

From this point, I can process the four integers and put them in such a tree (there are five kinds of such trees, why?-below), just modify the operator of the branch node, and then calculate. Get the result to see if it is equal to 24.

Therefore, an algorithm is derived: divide all integers into two non-empty parts, one as the left subtree of the root node, and the other as the right subtree of the root node, and recursively build the tree. When the tree is built, calculate the result again to see if it meets the requirements. This algorithm does not seem to be very difficult to implement, but after writing it, I found that the operation is not quite right. After debugging for a long time, I suddenly realized that there is a very ooxx problem with this kind of recursion: how to judge that the left subtree has been built? How to build the right subtree immediately after it is established? In fact, the process of recursively building the left subtree is to build (traverse) all possible left subtrees. When the function of building the left subtree returns, the left subtree is the last possible situation.

So this algorithm is wrong. As for how to fix it, I really can’t think of it (Is there anyone who can give advice?).

However, we can deduce that there are 5 kinds of calculation trees that may be formed by 4 integers, which are divided into 2 kinds of (1, 3), 1 kind of (2, 2), and 2 kinds of (3, 1) (because In the case of 3 integers, there are 2 kinds). The specific tree structure can be easily drawn, so I won’t expand it here (actually, I am too lazy to draw the picture and then post it, see the comment of the code below).

In this case, you can actually enumerate all the permutations of the 4 numbers, and then judge whether it is valid by enumerating the 3 symbols of the 5 trees and then calculating the results.

> Note that such a code has extremely poor scalability: it is only suitable for 4 digit cases.

Although this approach was frustrating, I still wrote the code (see the end of the article.)

Then I suddenly remember that a year ago, I searched a lot of solutions on the Internet and still stored in the hard disk, so I went to search and found a very good algorithm:

(1)Put 4 integers into an array, (2)In Take the arrangement of two numbers in the array, there are P(4,2) kinds of arrangements. For each permutation, (2.1) for +-* / for each operator, (2.1.1). According to this permutation of two numbers and operators, calculate the result, (2.1.2) Change the table array: this permutation change two numbers and remove them from the array, put the result of 2.1.1 calculation into the array, (2.1.3) Repeat step 2 for the new array, (2.1.4) Restore the array: the two in this arrangement The number is added to the array, and the result of 2.1.1 calculation is removed from the array.

It can be seen that step 2 is a recursive function. When there is only one number left in the array, this is the final result of the expression, and the recursion ends. This algorithm is implemented using backtracking, in fact it can be easily generalized to N numbers. (See the code at the end of the text).

Extended content:

1, 3, 4, 5, 6, 7, 8, 9 Add brackets and add, subtract, multiply and divide at will, but you can’t change it Sequence, the required result is equal to 1000.

On the basis of the last algorithm, writing this program should be quite simple, anyway, I will get the result soon.

Bad algorithm

```cpp
#include<iostream>
#include <cmath>

#defineN (4)
#defineBAD_NUM (-1e10)
#defineEPS (1e-9)
#define INVALID(t) (cmp(t, BAD_NUM) == 0)
int data[4] = {1, 2, 3, 4};
int d[4];
int v[4] = {0, 0, 0, 0};

void dfs(int i);
int cmp(const double &a, const double &b);
double op(double a, double b, int t);
char ops[] = "+-*/";

void judge(){
    int i, j, k;
    double ans;
    for (i = 0; i <4; ++i){
        for (j = 0; j <4; ++j){
            for(k = 0; k <4; ++k){
                // A~(B~(C~D))
                ans = op(d[0], op(d[1], op(d[2], d[3], k), j), i);
                if(cmp(ans, 24) == 0){
                    printf("%d%c(%d%c(%d%c%d))\n",
                            d[0],ops[i],d[1],ops[j],d[2],ops [k],d[3]);
                }
                // A~((B~C)~D)
                ans = op(d[0], op(op(d[1], d[2], j), d[3], k), i);
                if(cmp(ans, 24) == 0){
                    printf("%d%c((%d%c%d)%c%d)\n",
                            d[0],ops[i],d[1],ops[j],d[2],ops [k],d[3]);
                }
                // (A~B)~(C~D)
                ans = op(op(d[0], d[1], i), op(d[2], d[3], k), j);
                if(cmp(ans, 24) == 0){
                    printf("(%d%c%d)%c(%d%c%d)\n",
                            d[0],ops[i],d[1],ops[j],d[2],ops [k],d[3]);
                }
                // (A~(B~C))~D
                ans = op(op(d[0], op(d[1], d[2], j), i), d[3], k);
                if(cmp(ans, 24) == 0){
                    printf("(%d%c(%d%c%d))%c%d\n",
                            d[0],ops[i],d[1],ops[j],d[2],ops [k],d[3]);
                }
                // ((A~B)~C)~D
                ans = op(op(op(d[0], d[1], i), d[2], j), d[3], k);
                if(cmp(ans, 24) == 0){
                    printf("((%d%c%d)%c%d)%c%d\n",
                            d[0],ops[i],d[1],ops[j],d[2],ops[k],d[3]);
                }
            }
        }
    }
}

int main(){
    dfs(0);
    return 0;
}

void dfs(int i){ // enum all operations
    for (int j = 0; j < N; ++j){
        if (v[j] == 1) continue;
        v[j] = 1;
        d[i] = data[j];
        if (i == N - 1){
            //for (int t = 0; t < N; t++) printf("%d ", d[t]); printf("\n");
            judge(); // calaculate
        }else{
            dfs(i + 1);
        }
        v[j] = 0;
    }
}

int cmp(const double &a, const double &b){
    if (fabs(a - b) < EPS) return 0;
    if(a - b > 0) return 1;
    return-1;
}

double op(double a, double b, int t){
    if(INVALID(a) || INVALID(b)) return BAD_NUM;
    switch(t){
        case 0: // +
            return (a + b);
        case 1: // -
            return (a-b);
        case 2: // *
            return (a * b);
        case 3: // /
            if(cmp(b, 0.0) == 0) return BAD_NUM;
            return (a/b);
        default:
            returnBAD_NUM;
    }
}
```
Good algorithm:

```cpp
#include<iostream>
#include <cmath>

#defineN (4)
#define EPS (1e-9)
double d[N] = {3, 3, 8, 8};
string expr[N];

int cmp(const double &a, const double &b){
    if (fabs(a-b) <EPS) return 0;
    if(a-b> 0) return 1;
    return-1;
}

void dfs(int n){
    double a, b;
    string expa, expb;
    if(n == 1){
        if(cmp(d[0], 24) == 0){
            cout<< expr[0] << "=" << d[0] << endl;
        }
        return;
    }
    int i, j;
    for (i = 0; i <n; ++i){
        for(j = i + 1; j <n; ++j){
            a = d[i];
            b = d[j];
            d[j] = d[n-1];
            expa = expr[i];
            expb = expr[j];
            expr[j] = expr[n-1];

            //a + b
            d[i] = a + b;
            expr[i] = "(" + expa + "+" + expb + " )";
            dfs(n-1);

            //a-b
            d[i] = a-b;
            expr[i] = "(" + expa + "-" + expb + ")";
            dfs(n-1 );
            //b-a
            d[i] = b-a;
            expr[i] = "(" + expb + "-" + expa + ")";
            dfs(n-1);

            //a * b
            d[i] = a * b;
            expr[i] = "(" + expa + "*" + expb + ")";
            dfs(n-1);

            //a / b
            if(cmp(b, 0) != 0){
                d[i] = a / b;
                expr[i] = "(" + expa + "/" + expb + ")";
                dfs(n-1);
            }
            //b / a
            if(cmp(a, 0) != 0){
                d[i] = b / a;
                expr[i] = "(" + expb + "/" + expa + ")";
                dfs(n-1);
            }

            //Backtracking
            expr[j] = expb;
            expr[i] = expa;
            d[j] = b;
            d[i] = a;
        }
    }
}


int main(){
    for (inti = 0; i <N; ++i){
        expr[i] = (int)d[i] + '0';
    }
    dfs(N);
    return0;
}
```
