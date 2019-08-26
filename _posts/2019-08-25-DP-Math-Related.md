---
layout: post
title: Leetcode - Math Related
subtitle: LC813, LC837, LC1058
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



### 1058. Minimize Rounding Error to Meet Target

这题的trick是转换为N个数有多少个取floor多少个取ceil的dp。因为floor和ceil差1，而cost就转换为ceil比floor多出来的误差。O(N), O(N).

```java
class Solution {
    public String minimizeError(String[] prices, int target) {
        if(prices == null || prices.length == 0)    return "-1";
        int n = prices.length;
        double[] diff = new double[n];
        double top = 0, bottom = 0, sum = 0;
        for(int i = 0; i < n; i++) {
            double num = Double.parseDouble(prices[i]);
            double ceil_diff = Math.ceil(num) - num;
            double floor_diff = num - Math.floor(num);
            diff[i] = ceil_diff - floor_diff;
            top += Math.ceil(num);
            bottom += Math.floor(num);
            sum += floor_diff;
        }
        if((double) target < bottom || (double) target > top)   return "-1";
        int ceil_num = target - (int) bottom;
        Arrays.sort(diff);
        while(ceil_num-- > 0)   sum += diff[ceil_num];
        return String.format ("%.3f", sum);
    }
}
```

