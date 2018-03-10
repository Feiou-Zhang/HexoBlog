---
title: Binary Search
date: 2018-03-09 13:41:17
tags: [二分]
categories: [Algorithm]
---
some good practice to do a binary search:

The idea is use binary search to narrow down the range(options).

1. it is very important use `(right - left) / 2 + left` instead of using (left + right) / 2, because the sum of the left and right may over the max value of int
2. same thing for the left + 1 < right, and it will make the binary search so much easier, after the while loop, if did not return anything, the answer will fall into either the left or right.

```java
int left = 0,
    right = nums.length - 1;
        
while(left + 1 < right){
    int mid = (right - left) / 2 + left;

    if(nums[mid] == target) return mid;

    if(nums[mid] < target){
        left = mid;
    }else{
        right = mid;
    }
}
if(nums[left] >= target) return left;
else if(nums[right] >= target) return right;
else return right + 1;

```