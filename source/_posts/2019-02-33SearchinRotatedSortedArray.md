---
title: 33.Search in Rotated Sorted Array
date: 2019-02-02 14:59:14
categories: 
- Leetcode
- Binary Search
tags: 
- Leetcode
- Binary Search
---



# 题目描述

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`).

You are given a target value to search. If found in the array return its index, otherwise return `-1`.

You may assume no duplicate exists in the array.

Your algorithm's runtime complexity must be in the order of *O*(log *n*).

**Example 1:**

```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```

**Example 2:**

```
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
```



## 思路

根据以下几个步骤进行分类讨论：

```
1.如果target比A[mid]值要小
2.       如果A[mid]右边有序（A[mid]<A[high])
3.             那么target肯定不在右边（target比右边的都得小），在左边找
4.       如果A[mid]左边有序
5.             那么比较target和A[low]，如果target比A[low]还要小，证明target不6在这一区，去右边找；反之，左边找。
6.如果target比A[mid]值要大
7.      如果A[mid]左边有序（A[mid]>A[low]）
8.            那么target肯定不在左边（target比左边的都得大），在右边找
9.      如果A[mid]右边有序
10.           那么比较target和A[high]，如果target比A[high]还要大，证明target不在这一区，去左边找；反之，右边找。

```

```java
public class SearchinRotatedSortedArray {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return -1;
        }

        int low = 0;
        int high = nums.length - 1;

        while (low <= high) {
            int mid = low + ((high - low) >> 1);
            if (target < nums[mid]) {
                if (nums[mid] < nums[high]) {
                    high = mid - 1;
                } else {
                    if (target < nums[low]) {
                        low = mid + 1;
                    } else {
                        high = mid - 1;
                    }
                }
            } else if (target > nums[mid]) {
                if (nums[mid] > nums[low]) {
                    low = mid + 1;
                } else {
                    if (target > nums[high]) {
                        high = mid - 1;
                    } else {
                        low = mid + 1;
                    }
                }
            } else {
                return mid;
            }
        }
        return -1;
    }
}
```