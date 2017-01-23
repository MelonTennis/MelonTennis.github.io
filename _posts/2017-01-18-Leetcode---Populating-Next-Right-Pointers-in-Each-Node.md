---
layout: post
title: Leetcode - Populating Next Right Pointers in Each Node 
subtitle: LC116， LC117
date: 2017-01-18
categories: Leetcode
tags: [Tree]
catalog: true
---

#### LC116. Populating Next Right Pointers in Each Node

Given a binary tree

```
    struct TreeLinkNode {
      TreeLinkNode *left;
      TreeLinkNode *right;
      TreeLinkNode *next;
    }

```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`.

**Note:**

- You may only use constant extra space.
- You may assume that it is a perfect binary tree (ie, all leaves are at the same level, and every parent has two children).

For example,
Given the following perfect binary tree,

```
         1
       /  \
      2    3
     / \  / \
    4  5  6  7

```

After calling your function, the tree should look like:

```
         1 -> NULL
       /  \
      2 -> 3 -> NULL
     / \  / \
    4->5->6->7 -> NULL

```

利用递归，把每一棵树的左右连起来。由于是完美二叉树，所以可以直接连接左边的右和右边的左。

```java
/**
 * Definition for binary tree with next pointer.
 * public class TreeLinkNode {
 *     int val;
 *     TreeLinkNode left, right, next;
 *     TreeLinkNode(int x) { val = x; }
 * }
 */
public class Solution {
    public void connect(TreeLinkNode root) {
        if(root == null) return;
        connect(root.left, root.right);
    }
    
    public void connect(TreeLinkNode node1, TreeLinkNode node2){
        if(node1 == null){
            return;
        }
        node1.next = node2;
        if(node1.left != null){
        connect(node1.left, node1.right);
        connect(node2.left, node2.right);
        connect(node1.right, node2.left);
        }
    }
}       
```

O(n), O(height)

#### LC117. Populating Next Right Pointers in Each Node II 

Follow up for problem "*Populating Next Right Pointers in Each Node*".

What if the given tree could be any binary tree? Would your previous solution still work?

**Note:**

- You may only use constant extra space.

For example,
Given the following binary tree,

```
         1
       /  \
      2    3
     / \    \
    4   5    7

```

After calling your function, the tree should look like:

```
         1 -> NULL
       /  \
      2 -> 3 -> NULL
     / \    \
    4-> 5 -> 7 -> NULL
```

常数空间，就不能用递归啦。

> [Discuss](https://discuss.leetcode.com/topic/1106/o-1-space-o-n-complexity-iterative-solution/3)

每层进行处理，将一层的节点都链接好才进入下一层。

```java
/**
 * Definition for binary tree with next pointer.
 * public class TreeLinkNode {
 *     int val;
 *     TreeLinkNode left, right, next;
 *     TreeLinkNode(int x) { val = x; }
 * }
 */
public class Solution {
    public void connect(TreeLinkNode root) {
       // level by level link node
       TreeLinkNode temp = new TreeLinkNode(0);
       TreeLinkNode cur = null;
       while(root != null){
           cur = temp;
           // link current level
           while(root != null){
               if(root.left != null){
                   cur.next = root.left;
                   cur = cur.next;
               }
               if(root.right != null){
                   cur.next = root.right;
                   cur = cur.next;
               }
               root = root.next;
           }
           // next level
           root = temp.next;
           temp.next = null;
       } // while
       return;
    }
}
```

O(n), O(1).