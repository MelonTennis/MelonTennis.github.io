---
layout: post
title: Leetcode - Minimum Cost
subtitle: LC322, LC983, LC1105, LC1155, LC1130
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## Minimum Cost

很多求在各种情况下的最小消耗问题，大概是背包九讲的多重背包问题。

### 322. Coin Change

很简单的从所有可行状态中寻找最少的。O(N * M), O(1).

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        if(amount == 0) return 0;
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, amount + 1);
        dp[0] = 0;
        for(int i = 1; i <= amount; i++) {
            for(int j = 0; j < coins.length; j++) {
                if(coins[j] <= i)   dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
            }
        }
        return dp[amount] > amount ? -1: dp[amount];
    }
}
```



### 983. Minimum Cost For Tickets

这题和322没有什么区别，答案开了一个大小为365的数组然后根据coin change的解法解。我选择binary search。

```java
class Solution {
    public int mincostTickets(int[] days, int[] costs) {
        // O(n * d * lgn)
        // dp[i] = Math.min(dp[j] + cost[k]) j >= i - k
        if(days == null || days.length == 0 || costs == null || costs.length == 0)  return 0;
        int n = days.length;
        int[] ds = {1, 7, 30};
        int[] dp = new int[n + 1];
        for(int i = 1; i <= n; i++) {
            dp[i] = Integer.MAX_VALUE;
            for(int d = 0; d < ds.length; d++) {
                int f = find(days, ds[d], i - 1);
                if(f < i)  dp[i] = Math.min(dp[i], costs[d] + dp[f]);
                else dp[i] = Math.min(dp[i], dp[i - 1] + costs[0]);
            }
        }
        return dp[n];
    }
    
    private int find(int[] days, int delay, int idx) {
        // find first index >= t
        int t = days[idx] - delay + 1;
        if(t < days[0])  return 0;
        int l = 0, r = idx;
        while(l < r) {
            int mid = (l + r) / 2;
            if(days[mid] < t)   l = mid + 1;
            else r = mid;
        }
        return l;
    }
}

```



### 1105. Filling Bookcase Shelves

基本思想是一样的，这里也是找到所有可能的上一个状态，更新取最小值。O(N * N), O(N).

 ```java
class Solution {
    public int minHeightShelves(int[][] books, int shelf_width) {
        // O(n * n)
        // dp[i] = min(dp[i - 1] + height[i], dp[i - j] + max_height(j, i))
        if(books == null || books.length == 0 || shelf_width == 0)  return 0;
        int n = books.length;
        int[] dp = new int[n];
        for(int i = 0; i < n; i++) {
            int[] book = books[i];
            if(i == 0) {
                dp[i] = book[1];
            } else {
                dp[i] = dp[i - 1] + book[1];
                int j = i - 1;
                int width = book[0] + books[j][0];
                int height = Math.max(book[1], books[j][1]);
                while(width <= shelf_width && j >= 0) {
                    if(j > 0)   dp[i] = Math.min(dp[i], dp[j - 1] + height);
                    else dp[i] = Math.min(dp[i], height);
                    if(j == 0)  break;
                    j--;
                    width += books[j][0];
                    height = Math.max(height, books[j][1]);
                }
            }
        }
        return dp[n - 1];
    }
}
 ```



### 1155. Number of Dice Rolls With Target Sum

(a + b) % m = (a % m + b % m) % m。不要偷懒。

```java
class Solution {
    public int numRollsToTarget(int d, int f, int target) {
        // dp[i][j] = sum(dp[i - 1][j - k])  1 <= k <= f
        // O(d * target * f)
        int[][] dp = new int[d + 1][target + 1];
        int MOD = 1000000000 + 7;
        for(int i = 1; i <= d; i++) {
            for(int t = 1; t <= target; t++) {
                if(i == 1 && t <= f)    dp[i][t] = 1;
                else {
                    for(int k = 1; k <= Math.min(f, t); k++) {
                        dp[i][t] = (dp[i - 1][t - k] % MOD + dp[i][t] % MOD) % MOD;
                    }
                }
            }
        }
        return dp[d][target] % MOD;
    }
}
```



### 1130. Minimum Cost Tree From Leaf Values

这题难度还挺高的。。。分别可以用DP，Greedy，Stack解

####DP 解法

dp[i\][j]代表i到j个元素所能达到的最小值。那么根据题意dp[i\][j] = max(arr[i, k]) * max(arr[k+1, j]) + dp[i\][k] + dp[k+1\][j]. 而我在写DP的时候犯的一个错误是从i = 1, j = 0开始更新，但是这个题的更新是从**叶子结点**，也就是**length最小**开始更新。O(N * N *N), O(N * N).

```java
class Solution {
    public int mctFromLeafValues(int[] arr) {
        // dp O(N^3)
        // dp[i][j] = max(arr[i, k]) * max(arr[k+1, j]) + dp[i][k] + dp[k+1][j];
        int n = arr.length;
        int[][] dp = new int[n][n];
        int[][] max = new int[n][n];
        for(int i = 0; i < n; i++) {
            int m = arr[i];
            for(int j = i; j < n; j++) {
                if(arr[j] > m)    m = arr[j];
                max[i][j] = m;
            }
        }
        
        // Note that dp is updating from len = 1, not from left to right
        // Should for len then for left
        for(int len = 1; len < n; len++) {
            for(int i = 0; i + len < n; i++) {
                int j = i + len;
                dp[i][j] = Integer.MAX_VALUE;
                if(j == i + 1)  dp[i][j] = arr[i] * arr[j];
                else {
                    for(int k = i; k < j; k++) {
                        dp[i][j] = Math.min(max[i][k] * max[k + 1][j] + dp[i][k] + dp[k + 1][j], dp[i][j]);
                    }
                }
            }
        }
        return dp[0][n - 1];
    }
}
```

#### Greedy 解法

我们想要最小的结果，所以小的值一定尽可能的在叶结点，这样才能够每次乘法乘尽可能多的小值。所以可以每次选择最小的值作为叶结点的一个值，另一个值选择它比较小的邻居，同时更新结果并删去这个最小节点。直到最后只剩下一个节点。O(N * N), O(N).

```java
class Solution {
    public int mctFromLeafValues(int[] arr) {
        // greedy solution
        // want minimum solution
        // so min value must be in the deepest bottom
        // each time pick min value as leaf, then combine with smaller neighbour
        // delete min value since the value will be cover by a larger one
        // repeat until only one node
        List<Integer> list = new ArrayList<>();
        for(int a: arr) list.add(a);
        int sum = 0;
        while(list.size() > 1) {
            int size = list.size();
            int minIdx = 0;
            for(int i = 1; i < size; i++) {
                if(list.get(i) < list.get(minIdx))  minIdx = i;
            }
            int left = minIdx - 1, right = minIdx + 1;
            if(left < 0)    sum += list.get(minIdx) * list.get(right);
            else if(right == size)  sum += list.get(minIdx) * list.get(left);
            else {
                if(list.get(left) < list.get(right))    sum += list.get(left) * list.get(minIdx);
                else sum += list.get(right) * list.get(minIdx);
            }
            list.remove(minIdx);
        }
        return sum;
    }
}
```

#### Stack 解法

[Lee215的解法](<https://leetcode.com/problems/minimum-cost-tree-from-leaf-values/discuss/339959/One-Pass-O(N)-Time-and-Space>)

O(N), O(N).