---
layout: post
title: Leetcode - Special Pattern
subtitle: LC351, LC651, LC688, LC935
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## Special Pattern

有一些special pattern的限制的DP题。一般而言存一个map来表示可能的状态就可以。

### 351. Android Unlock Patterns

存了invalid path，并且利用对称性减少重复代码。是一个Backtrack问题。O(n!), O(1).

```java
class Solution {
    public int numberOfPatterns(int m, int n) {
        int[][] map = new int[10][10];
        map[1][3] = map[3][1] = 2;
        map[1][7] = map[7][1] = 4;
        map[3][9] = map[9][3] = 6;
        map[7][9] = map[9][7] = 8;
        map[1][9] = map[9][1] = map[7][3] = map[3][7] = 5;
        map[2][8] = map[8][2] = map[4][6] = map[6][4] = 5;
        
        int res = 0;
        for(int i = m; i <= n; i++) {
            res += find(map, new boolean[10], 1, i) * 4;
            res += find(map, new boolean[10], 2, i) * 4;
            res += find(map, new boolean[10], 5, i);
        }
        return res;
    }
    
    private int find(int[][] map, boolean[] visit, int plc, int n) {
        if(n == 1)  return 1;
        visit[plc] = true;
        int res = 0;
        for(int i = 1; i <= 9; i++) {
            if(!visit[i] && (map[plc][i] == 0 || visit[map[plc][i]])) {
                res += find(map, visit, i, n - 1);
            }
        }
        visit[plc] = false;
        return res;
    }
}
```



### 651. 4 Keys Keyboard

这题好像放错位置了。。。O(N * N), O(1)

```java
class Solution {
    public int maxA(int N) {
        if(N == 0)  return 0;
        int[] dp = new int[N + 1];
        for(int i = 0; i <= N; i++) {
            dp[i] = i;
            for(int j = 0; j < N - 2; j++) {
                dp[i] = Math.max(dp[i], dp[j] * (i - j - 1));
            }
        }
        return dp[N];
    }
}
```



### 688. Knight Probability in Chessboard

同样存上所有可能的方向，然后更新状态。O(N * N * K), O(N * N).

```java
class Solution {
    public double knightProbability(int N, int K, int r, int c) {
        double[][] dp = new double[N][N];
        int[] dx = {2, 2, 1, 1, -1, -1, -2, -2};
        int[] dy = {1, -1, 2, -2, 2, -2, 1, -1};
        dp[r][c] = 1;
        while(K-- > 0) {
            double[][] next = new double[N][N];
            for(int i = 0; i < N; i++) {
                for(int j = 0; j < N; j++) {
                    for(int k = 0; k < 8; k++) {
                        int x = i + dx[k];
                        int y = j + dy[k];
                        if(x >= 0 && x < N && y >= 0 && y < N) {
                            next[x][y] += dp[i][j] / 8.0;
                        }
                    }
                }
            }
            dp = next;
        }
        double res = 0;
        for(int i = 0; i < N; i++) {
            for(int j = 0; j < N; j++) {
                res += dp[i][j];
            }
        }
        return res;
    }
}
```



### 935. Knight Dialer

同理，O(N), O(1).

```java
class Solution {
    public int knightDialer(int N) {
        if(N == 0)  return 0;
        int[][] step = {{4, 6}, {6, 8}, {7, 9}, {4, 8}, {0, 9, 3}, {}, {1, 7, 0}, 
                        {2, 6}, {1, 3}, {2, 4}};
        int[] dp = new int[10];
        int MOD = 1000000000 + 7;
        Arrays.fill(dp, 1);
        while(N-- > 1) {
            int[] next = new int[10];
            for(int i = 0; i < 10; i++) {
                for(int p: step[i]) {
                    next[p] = (next[p] % MOD + dp[i] % MOD) % MOD;
                }
            }
            dp = next;
        }
        int res = 0;
        for(int i = 0; i < 10; i++)  res = (dp[i] % MOD + res % MOD) % MOD;
        res = res % MOD;
        return res;
    }
}
```

