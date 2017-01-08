---
layout: post
title: Leetcode - Path Sum problems
subtitle: LC112, 
date: 2017-01-07
categories: Leetcode
tags: [Tree, D&C]
catalog: true
---

#### LC112. Path Sum

Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.

判断一棵树是否有一条跟到叶的路径之和为某一数字。DFS. O(n).

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
    public boolean hasPathSum(TreeNode root, int sum) {
        if(root == null)
            return false;
        if(sum == root.val && root.left == null && root.right == null)
            return true;
        return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
    }
}
```

#### LC113. Path Sum II

Given a binary tree and a sum, find all root-to-leaf paths where each path's sum equals the given sum.

👆把所有的路径都输出。DFS，Backtracking, O(n)吧.

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
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if(root == null){
            return res;
        }
        List<Integer> list = new ArrayList<Integer>();
        travel(res, root, sum, list);
        return res;
    }
    
    public void travel(List<List<Integer>> res, TreeNode root, int sum, List<Integer> list){
        if(root == null){
            return;
        }
        else if(root.val == sum && root.left == null && root.right == null){
            list.add(root.val);
            res.add(new ArrayList(list));
            list.remove(list.size()-1);
        }else{
            list.add(root.val);
            travel(res, root.right, sum-root.val, list);
            list.remove(list.size()-1);
            list.add(root.val);
            travel(res, root.left, sum-root.val, list);
            list.remove(list.size()-1);
        }
    }
}
```

#### LC437. Path Sum III

You are given a binary tree in which each node contains an integer value.

Find the number of paths that sum to a given value.

The path does not need to start or end at the root or a leaf, but it must go downwards (traveling only from parent nodes to child nodes).

##### DFS solution:

不一定从根到叶，同样的也是向左和向右分别搜索。O(n^2).

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
    public int pathSum(TreeNode root, int sum) {
        if(root == null)    return 0;
        int res = 0;
        if(root.left != null) {
            res += pathSum(root.left, sum);
        }
        if(root.right != null)  {
            res += pathSum(root.right, sum);
        }
        res += find(root, sum);
        return res;
    }
    
    public int find(TreeNode node, int sum){
        if(node == null)    return 0;
        int res = 0;
        if(sum == node.val) res++;
        res += find(node.left, sum - node.val);
        res += find(node.right, sum - node.val);
        return res;
    }
}
```

##### Prefix solution:

> [Prefix O(n)](https://discuss.leetcode.com/topic/64526/17-ms-o-n-java-prefix-sum-method)

和[Two Sum](https://yijiajin.github.io/leetcode/2017/01/04/Leetcode-Array-Sum-Problems/)那个系列的解法相似，HashMap存[preSum, numbers of ways], 每次遇到一个node，找hashmap里有没有sum - target存在，如果存在就res += map.get(sum - target). O(n)解法

```java
  public int pathSum(TreeNode root, int sum) {
        HashMap<Integer, Integer> preSum = new HashMap();
        preSum.put(0,1);
        helper(root, 0, sum, preSum);
        return count;
    }
    int count = 0;
    public void helper(TreeNode root, int sum, int target, HashMap<Integer, Integer> preSum) {
        if (root == null) {
            return;
        }
        
        sum += root.val;

        if (preSum.containsKey(sum - target)) {
            count += preSum.get(sum - target);
        }
        
        if (!preSum.containsKey(sum)) {
            preSum.put(sum, 1);
        } else {
            preSum.put(sum, preSum.get(sum)+1);
        }
        
        helper(root.left, sum, target, preSum);
        helper(root.right, sum, target, preSum);
        preSum.put(sum, preSum.get(sum) - 1);
    }
```

⚠️：要保证向下遍历时map里的是以当前节点为结尾的path，所以在查找过左右子节点后要去掉map中当前sum的一个值。~~我觉得我只能想出来dfs~~