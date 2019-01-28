---
title: 206.Reverse Linked List
date: 2019-01-28 16:55:53
categories: 
- Leetcode
- 链表
tags: 
- Leetcode
- 链表
---

## 题目描述

Reverse a singly linked list.

Example:
Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL

Follow up:
A linked list can be reversed either iteratively or recursively. Could you implement both?



## 思路一

使用**循环**。

```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    ListNode curr = head;
    while (curr != null) {
        ListNode nextTemp = curr.next;
        curr.next = prev;
        prev = curr;
        curr = nextTemp;
    }
    return prev;
}
```

