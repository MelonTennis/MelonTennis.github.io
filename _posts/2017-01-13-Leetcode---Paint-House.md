---
layout: post
title: Leetcode - Paint House
subtitle: LC256, LC265
date: 2017-01-13
categories: Leetcode
tags: [DP]
catalog: true
---

#### LC256. Paint House 

There are a row of *n* houses, each house can be painted with one of the three colors: red, blue or green. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.

The cost of painting each house with a certain color is represented by a `n x 3` cost matrix. For example, `costs[0][0]` is the cost of painting house 0 with color red; `costs[1][2]` is the cost of painting house 1 with color green, and so on... Find the minimum cost to paint all houses.

**Note:**
All costs are positive integers.

DP解，每一个状态包括三种颜色，最后取最小值。

```java
public class Solution {
    public int minCost(int[][] costs) {
        if(costs == null || costs.length == 0)  return 0;
        int[] cache = new int[3];
        int[] dp = new int[3];
        for(int i = 0; i < 3; i++)  cache[i] = costs[0][i];
        for(int i = 1; i < costs.length; i++){
            dp[0] = Math.min(cache[1], cache[2]) + costs[i][0];
            dp[1] = Math.min(cache[2], cache[0]) + costs[i][1];
            dp[2] = Math.min(cache[1], cache[0]) + costs[i][2];
            cache = dp.clone();
        }
        return Math.min(Math.min(cache[0], cache[1]), cache[2]);
    }
}
```

O(nk), O(k)

#### LC265. Paint House II

There are a row of *n* houses, each house can be painted with one of the *k* colors. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.

The cost of painting each house with a certain color is represented by a `n x k` cost matrix. For example, `costs[0][0]` is the cost of painting house 0 with color 0; `costs[1][2]` is the cost of painting house 1 with color 2, and so on... Find the minimum cost to paint all houses.

**Note:**
All costs are positive integers.

**Follow up:**
Could you solve it in *O*(*nk*) runtime?

唔，那我把dp数组设大一点好了。

```java
public class Solution {
    public int minCostII(int[][] costs) {
        if(costs == null || costs.length == 0)  return 0;   
        int n = costs.length, k = costs[0].length;
        int[] dp = new int[k];
        int[] cache = new int[k];
        for(int i = 0; i < k; i++){
            cache[i] = costs[0][i];
        }
        for(int i = 1; i < n; i++){
            for(int j = 0; j < k; j++)  dp[j] = findMin(cache, j) + costs[i][j];
            cache = dp.clone();
        }
        return findMin(cache, -1);
    }
    
    private int findMin(int[] array, int pos){
        int res = Integer.MAX_VALUE;
        for(int i = 0; i < array.length; i++){
            if(i != pos)    res = Math.min(res, array[i]); 
        }
        return res;
    }
}
```

这是一个naive的O(nk^2), O(k)解法

##### Follow up

> [O(1) space](https://discuss.leetcode.com/topic/30659/easiest-o-1-space-java-solution)

还是看了discuss，O(nk)+O(1)解法，每个房子只要找到最小的两个值就可以了，除非最小值和前一个颜色相同用次小值，否则用最小值。PS.这一优化方法在背包九讲中有介绍过。

```java
public class Solution {
    public int minCostII(int[][] costs) {
        if(costs == null || costs.length == 0)  return 0;   
        int n = costs.length, k = costs[0].length;
        int min1 = 0, min2 = 0, index = -1;
        for(int i = 0; i < n; i++){
            int m1 = Integer.MAX_VALUE, m2 = Integer.MAX_VALUE, idx1 = -1;
            for (int j = 0; j < k; j++) {
                int cost = costs[i][j] + (j != index? min1 : min2);
                if (cost < m1) {           // cost < m1 < m2
                    m2 = m1; m1 = cost; idx1 = j; 
                } else if (cost < m2) {    // m1 < cost < m2
                    m2 = cost;
                }
            } // for j
            min1 = m1; 
            min2 = m2;
            index = idx1;
        } // for i
        return min1;
    }
}
```
