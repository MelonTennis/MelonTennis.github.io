---
layout: post
title: Leetcode - Longest Subarray and Substring
subtitle: LC718, LC647
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## Longest Subarray and Substring

Subarray & Substring是连续的，比起subsequence的状态转换要简单一些。经常是O(N * N), O(N)的解法。

### 718. Maximum Length of Repeated Subarray

```java
class Solution {
    public int findLength(int[] A, int[] B) {
        if(A == null || B == null)  return 0;
        int m = A.length, n = B.length;
        int[][] dp = new int[m][n];
        int res = 0;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(i* j == 0 && A[i] == B[j]) {
                    dp[i][j] = 1;
                } else if(A[i] == B[j]) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                    res = Math.max(dp[i][j], res);
                }
            }
        }
        return res;
    }
}
```



### 647. Palindromic Substrings

```java
class Solution {
    public int countSubstrings(String s) {
        if(s == null || s.length() == 0)    return 0;
        char[] arr = s.toCharArray();
        int res = 0;
        for(int i = 0; i < arr.length; i++) {
            res += find(arr, i, i);
            if(i + 1 < arr.length) res += find(arr, i, i + 1);
        }
        return res;
    }

    private int find(char[] arr, int l, int r) {
        if(arr[l] != arr[r])    return 0;
        int res = 1;
        l--;
        r++;
        while(l >= 0 && r < arr.length) {
            if(arr[l] == arr[r]) {
                l--;
                r++;
                res += 1;
            } else {
                break;
            }
        }
        return res;
    }
}


```



### 1055. Shortest Way to Form String

```java
class Solution {
    public int shortestWay(String source, String target) {
        if(source == null || target == null)    return -1;
        char[] s = source.toCharArray();
        char[] t = target.toCharArray();
        int res = 0, i = 0;
        while(i < t.length) {
            int l = i, r = 0;
            while(r < s.length && l < t.length) {
                if(t[l] == s[r]) {
                    l++;
                    r++;
                } else {
                    r++;
                }
            }
            if(l == i)  return -1;
            res++;
            i = l;
        }
        return res;
    }
}
```

