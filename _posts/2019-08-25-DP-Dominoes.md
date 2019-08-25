---
layout: post
title: Leetcode - Dominoes
subtitle: LC833, 
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## Dominoes

### 838. Push Dominoes

这题蛮好的，问推倒多米诺之后的结果。然后用two pointer做了。对于每一段多米诺。如果这一段左右状态相同，那么这一段都是这个状态。否则根据这一段的长度是否为奇数更新结果中的相应部分。O(N), O(1).

```java
class Solution {
    public String pushDominoes(String dominoes) {
        if(dominoes == null || dominoes.length() == 0)  return "";
        StringBuilder sb = new StringBuilder();
        dominoes = "L" + dominoes + "R";
        int n = dominoes.length();
        for(int i = 0, j = 1; j < n; j++) {
            if(dominoes.charAt(j) == '.') {
                continue;
            }
            int len = j - 1 - i;
            if(i > 0) {
                sb.append(dominoes.charAt(i));
            }
            if(dominoes.charAt(i) == dominoes.charAt(j)) {
                for(int k = 0; k < len; k++)    sb.append(dominoes.charAt(i));
            } else {
                if(dominoes.charAt(i) == 'L') {
                    for(int k = 0; k < len; k++)    sb.append('.');
                } else {
                    for (int k = 0; k < len / 2; k++) sb.append('R');
                    if (len % 2 == 1) sb.append('.');
                    for (int k = 0; k < len / 2; k++) sb.append('L');
                }
            }
            i = j;
        }
        return sb.toString();
    }
}
```



### 790. Domino and Tromino Tiling

这题的状态转换，根据[discuss1](<https://leetcode.com/problems/domino-and-tromino-tiling/discuss/116612/Easy-to-understand-O(n)-solution-with-Drawing-Picture-Explanation!>), [discuss2](<https://leetcode.com/problems/domino-and-tromino-tiling/discuss/116664/Schematic-explanation-of-two-equivalent-DP-recurrence-formula>), 是dp[n] = dp[n - 1] * 2 + dp[n - 3] * 3. O(N), O(1). 但是很容易想错 。。。

```java
class Solution {
    public int numTilings(int N) {
        if(N == 0)  return 0;
        if(N == 1)  return 1;
        if(N == 2)  return 2;
        int MOD = 1000000007;
        int[] dp = new int[N + 1];
        dp[0] = 1;
        dp[1] = 1;
        dp[2] = 2;
        for(int i = 3; i <= N; i++) {
            dp[i] = (2 * dp[i - 1] % MOD + dp[i - 3] % MOD) % MOD;
        }
        return dp[N];
    }
}
```

