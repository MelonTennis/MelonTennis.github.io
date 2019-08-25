---
layout: post
title: Leetcode - Unique BST
subtitle: LC95，LC96
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## Unique BST

### 96. Unique Binary Search Trees

自底向上的dp解法，O(N * N), O(N).

```java
class Solution {
    public int numTrees(int n) {
        Map<Integer, Integer> map = new HashMap<>();
        if(n == 0)  return 0;
        map.put(1, 1);
        map.put(0, 1);
        return find(map, n);
    }
    
    private int find(Map<Integer, Integer> map, int n) {
        if(map.containsKey(n))  return map.get(n);
        int res = 0;
        for(int i = 0; i < n; i++) {
            res += find(map, i) * find(map, n - 1 - i);
        }
        map.put(n, res);
        return res;
    }
}
```



### 95. Unique Binary Search Trees II

在96的基础上把它们打出来，一样的。

```java
class Solution {
    public List<TreeNode> generateTrees(int n) {
        if(n == 0)  return new ArrayList<TreeNode>();
        return helper(1, n);
    }
    
    private List<TreeNode> helper(int start, int end) {
        List<TreeNode> list = new ArrayList<TreeNode>();
        if(start > end) {
            list.add(null);
            return list;
        }
        if(start == end) {
            list.add(new TreeNode(start));
            return list;
        }
        for(int i = start; i <= end; i++) {
            List<TreeNode> left = helper(start, i - 1);
            List<TreeNode> right = helper(i + 1, end);          
            for(TreeNode l: left) {
                for(TreeNode r: right) {
                    TreeNode root = new TreeNode(i);
                    root.left = l;
                    root.right = r;
                    list.add(root);
                }
            }
        }
        return list;
    }
}
```

