---
title: 144.Binary Tree Preorder Traversal前序遍历
date: 2019-02-01 14:48:30
categories: 
- Leetcode
- Tree
tags: 
- Leetcode
- Tree
---



# 题目描述

Given a binary tree, return the *preorder* traversal of its nodes' values.

**Example:**

```
Input: [1,null,2,3]
   1
    \
     2
    /
   3

Output: [1,2,3]
```

**Follow up:** Recursive solution is trivial, could you do it iteratively?



# 思路一

使用递归的方式



```java
public class BinaryTreePreorderTraversal {

    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int x) {
            val = x;
        }
    }

    public List<Integer> list = new ArrayList<Integer>();
    public List<Integer> preorderTraversal(TreeNode root) {
        preOrder(root);
        return list;
    }

    private void preOrder(TreeNode node) {
        if (node == null) {
            return;
        }
        list.add(node.val);
        if (node.left != null) {
            preOrder(node.left);
        }
        if (node.right != null) {
            preOrder(node.right);
        }
    }
}

```



# 思路二

使用循环的方式，利用栈来存储节点。



```java
public class BinaryTreePreorderTraversal {

    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode(int x) {
            val = x;
        }
    }

    public List<Integer> list = new ArrayList<Integer>();
    public List<Integer> preorderTraversalTwo(TreeNode root) {
        if (root == null) {
            return list;
        }
        Stack<TreeNode> stack = new Stack<TreeNode>();
        stack.push(root);
        TreeNode tempNode = null;
        while (!stack.isEmpty()) {
            tempNode = stack.pop();
            list.add(tempNode.val);
            if (tempNode.right != null) {
                //先压右边，先压后出
                stack.push(tempNode.right);
            }
            if (tempNode.left != null) {
                //再压左边，后压先出
                stack.push(tempNode.left);
            }
        }
        return list;
    }
}

```

