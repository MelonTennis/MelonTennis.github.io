---
layout: post
title: Leetcode - Math Related
subtitle: LC813, LC837
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## Math Related DP

数学不好orz。这类DP的状态转换不是很好找。

### 813. Largest Sum of Averages

O(N * N * K), O(N * K).

```java
class Solution {
    public double largestSumOfAverages(int[] A, int K) {
        // dp[i][k] = max_j(dp[j, k - 1] + avg(A[j, i]))
        int n = A.length;
        double[] pre_sum = new double[n];
        pre_sum[0] = (double)A[0];
        for(int i = 1; i < n; i++) {
            pre_sum[i] = pre_sum[i - 1] + (double)A[i];
        }
        double[][] dp = new double[n][K + 1];
        for(int k = 1; k <= K; k++) {
            for(int i = 0; i < n; i++) {
                if(k == 1) {
                    dp[i][k] = pre_sum[i] / (i + 1);
                } else if(k > i + 1) {
                    continue;
                } else {
                    for(int j = k - 1; j <= i; j++) {
                        dp[i][k] = Math.max(dp[j - 1][k - 1] + (pre_sum[i] - pre_sum[j - 1])/(i - j + 1), dp[i][k]);
                    }
                }
                
            }
        }
        return dp[n - 1][K];
    }
}
```



### 837. New 21 Game

O(N + W), O(N). 

```java
class Solution {
    public double new21Game(int N, int K, int W) {
        if(N >= W + K || K == 0)    return 1;
        // dp[i] means p(total points reach i)
        double[] dp = new double[N + 1];
        dp[0] = 1;
        // dp[0], wsum is sum(dp[i - W] + ... + dp[i - 1])
        double wsum = 1, res = 0;
        for(int i = 1; i < N + 1; i++) {
            dp[i] = wsum / W;
            if(i < K)   wsum += dp[i];
            else res += dp[i];
            if(i >= W)  wsum -= dp[i - W];
        }
        return res;
    }
}
```