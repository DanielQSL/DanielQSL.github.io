---
title: 905.Sort Array By Parity 按奇偶校验排序数组
date: 2019-01-06 17:52:11
categories: 
- Leetcode
- 数组
tags: 
- Leetcode
- 算法
- 数组
---

## 题目描述

Given an array A of non-negative integers, return an array consisting of all the even elements of A, followed by all the odd elements of A.

You may return any answer array that satisfies this condition.

给定一个非负整数数组 `A`，返回一个由 `A` 的所有偶数元素组成的数组，后面跟 `A` 的所有奇数元素。

你可以返回满足此条件的任何数组作为答案。

 

**Example 1:**

```
Input: [3,1,2,4]
Output: [2,4,3,1]
The outputs [4,2,3,1], [2,4,1,3], and [4,2,1,3] would also be accepted.
```

**提示：**

1. `1 <= A.length <= 5000`
2. `0 <= A[i] <= 5000`



## 解题思路一

定义一个新数组，偶数放在新数组的前面，奇数放在新数组的最后。

```java
public int[] sortArrayByParity(int[] A) {
    int[] arr = new int[A.length];
    int begin = 0;
    int end = A.length - 1;
    for (int i = 0; i < A.length; i++) {
        if (A[i] % 2 == 0) {
            arr[begin++] = A[i];
        } else {
            arr[end--] = A[i];
        }
    }
    return arr;
}
```



## 解题思路二

第一种思路很简单使用了新数组，这里说下第二种，在不使用新数组，就地操作（in-place），也称为原位操作。

保持两个指针i和j，循环变量j，将偶数值的索引与当前值的索引进行互换。

```java
public int[] sortArrayByParity(int[] A) {
    for (int i = 0, j = 0; j < A.length; j++) {
        if (A[j] % 2 == 0) {
            int tmp = A[i];
            A[i++] = A[j];
            A[j] = tmp;
        }
    }
    return A;
}
```