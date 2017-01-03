---
layout: post
title: Leetcode - Closest Binary Search Tree Value I/II 
subtitle: LC270, LC272
date: 2017-01-02
categories: Leetcode
tags: [BST, Tree, Stack]
catalog: true
---

### LC270. Closest Binary Search Tree Value

Given a non-empty binary search tree and a target value, find the value in the BST that is closest to the target.

首先是easy题，递归求解即可。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public int closestValue(TreeNode root, double target) {
        int a = root.val;
        TreeNode kid = (a>target?root.left:root.right);
        if(kid == null) return a;
        int b = closestValue(kid, target);
        return Math.abs(a - target) < Math.abs(b - target) ? a : b;
    }
}
```

O(lgn)

### LC272 Closest Binary Search Tree Value II 

Given a non-empty binary search tree and a target value, find *k* values in the BST that are closest to the target.

**Note:**

- Given target value is a floating point.
- You may assume *k* is always valid, that is: *k* ≤ total nodes.
- You are guaranteed to have only one unique set of *k* values in the BST that are closest to the target.

> [Java with two stacks](https://discuss.leetcode.com/topic/22940/ac-clean-java-solution-using-two-stacks)

求k个最近的值，求一个最近的值是分别找到大于和小于目标值的节点进行比较，所以求k个值也是找到大于和小于节点的值分别进行比较取前k个差最小的。利用两个栈+inorder遍历可以得到所有比target小／大的节点值，取前k个即可。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public List<Integer> closestKValues(TreeNode root, double target, int k) {
        // get nearest larger node: right->left -> reverse inorder
        // get nearest smaller ndoe: left->right -> inorder
        // use two stack to save successor and predecessor
        List<Integer> res = new ArrayList<Integer>();
        Stack<Integer> pre = new Stack<Integer>();
        Stack<Integer> suc = new Stack<Integer>();
        inorder(root, target, pre, false);
        inorder(root, target, suc, true);
        while(k > 0){
            if(pre.isEmpty())   res.add(suc.pop());
            else if(suc.isEmpty())  res.add(pre.pop());
            else if(Math.abs(pre.peek() - target) < Math.abs(suc.peek() - target))  res.add(pre.pop());
            else    res.add(suc.pop());
            k--;
        }
        return res;
    } // closest
    
    private void inorder(TreeNode node, double target, Stack<Integer> stack, boolean reverse){
        if(node == null)    return;
        if(reverse){
            inorder(node.right, target, stack, reverse);
            if(node.val <= target)  return;
            stack.push(node.val);
            inorder(node.left, target, stack, reverse);
        }else{
            inorder(node.left, target, stack, reverse);
            if(node.val > target)  return;
            stack.push(node.val);
            inorder(node.right, target, stack, reverse);
        }
    } // inorder
}
```

⚠️： inorder遍历的函数里面，reverse部分和非reverse部分只有一边带=。

复杂度：inorder: O(n),  find k in two stacks: O(k), total O(n + k)

**Follow up:**

> [O(n) and O(lgn) solution](https://discuss.leetcode.com/topic/29438/2-ms-o-n-and-6-ms-o-logn-java-solution)

O(n) 解法也是利用中序遍历维护一个长度为k的升序List，当k比较大时还是比较有优势。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public List<Integer> closestKValues(TreeNode root, double target, int k) {
        // maintain a list of size k
        List<Integer> list = new LinkedList<Integer>();
        helper(root, list, target, k);
        return list;
    } // closest
    
    private void helper(TreeNode root, List<Integer> list, double target, int k){
        if(root == null || (list.size() == k && list.get(0) >= target)) return;
        helper(root.left, list, target, k);
        if(list.size() == k && list.get(0) < target && Math.abs(target - list.get(0)) > Math.abs(root.val - target)){
            list.remove(0);
            list.add(root.val);
        }else if(list.size() < k){
            list.add(root.val);
        }
        helper(root.right, list, target, k);
    } // helper
}

```

