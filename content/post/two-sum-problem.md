+++
title = "Two Sum Problem"
description = ""
tags = [
    "array", "hash table",
]
date = "2019-08-25"
categories = [
    "dp",
]
+++

Given an `array(array)` and a `number(sum)`, find the two numbers from the array that sums up the given number.

Example:

> array : [1,2,6,5,8,9,17,10] , sum : 12

Answer for above example would be `[2,10]` , if no such numbers found return `[-1,-1]`.

The approach to solve the given problem is to loop through the given array twice and make a simple check. If the sum of the numbers (first & second loop) is equal to the given sum, the code snippet below will make it clearer.

```c
public int[] twoSum(int[] array, int sum) {
  for(int first = 0;first<array.length;first++){
     int firstNum = array[first];
     for(int second = first+1;second < array.length;second++){
       if(array[second] + firstNum == sum)
         return new int[]{firstNum , array[second]}; 
     } 
   }
  return new int[]{-1,-1};
}
```

Time Complexity : `O(n^2)` , `n – size of array`

Space Complexity : `O(1)`

Solving the problem wastes a tedious amount of time. Let's find some better approach.

If two numbers from the array are `x` and `y` then `x + y = sum`, that leads to the equation `y = sum - x`. Using this equation to solve the problem. Storing the array elements into a hash map and while traversing through array , I’ll check if `y` is present or not in the hash map, If it’s present that means we have found the `x` and `y` values.

```c
public int[] twoSum(int[] array, int sum) {
  Map<Integer , Boolean> markedNums = new HashMap<>();
  for(int first = 0;first<array.length;first++){
    int y = sum - array[first];
    if(markedNums.containsKey(y))
      return y > array[first] ? new int[]{array[first],y} : new int[]{y,array[first]};
    else
     markedNums.put(array[first],true); 
  }
  return new int[]{-1,-1};
}
```

Time Complexity : `O(n) , n – size of array`

Space Complexity : `O(n)`
