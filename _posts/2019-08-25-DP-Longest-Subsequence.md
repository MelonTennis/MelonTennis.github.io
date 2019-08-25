---
layout: post
title: Leetcode - Longest Subsequence
subtitle: LC300, LC673, LC516, LC1027, LC873, LC368
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## Longest Subsequence

### 300. Longest Increasing Subsequence

这堆题开始的地方。。。寻找最长递增子序列。这题的思路是维护一个lis序列，如果可以递增的增加数字进去，就增加，如果不可以的话就把lis里的数字更新。通过每次与res进行比较就能得到最大值。但是lis最终的结果可能不是一个subsequence。可以由O(N * N)优化到O(N * lgN)，空间O(N).

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
         // https://www.cnblogs.com/grandyang/p/4938187.html
         // dp + binary search O(nlgn)
        if(nums == null || nums.length == 0) {
            return 0;
        }
        int[] lis = new int[nums.length];
        int size = 0;
        for(int i = 0; i < nums.length; i++) {
            if(size == 0 || lis[size - 1] < nums[i]) {
                lis[size++] = nums[i];
            } else {
                // binary search first number no less than nums[i] 
                int l = 0, r = size;
                while(l < r) {
                    int mid = l + (r - l) / 2;
                    if(lis[mid] < nums[i]) {
                        l = mid + 1;
                    } else {
                        r = mid;
                    }
                }
                lis[l] = nums[i];
            }
        }
        return size;
    }
}
```



### 673. Number of Longest Increasing Subsequence

300的基础上求能得到全局最优的数量。换回普通的O(N * N)解法然后额外保持一个数组记录lis长度为i的数量。O(N * N), O(N). 同样的用Segment Tree可以优化到O(N * lgN). LC崩了。留着Segment Tree再看。

```java
class Solution {
    public int findNumberOfLIS(int[] nums) {
        int n = nums.length, max = 0, res = 0;
        int[] length = new int[n];
        int[] count = new int[n];
        Arrays.fill(count, 1);
        Arrays.fill(length, 1);
        for(int i = 0; i < nums.length; i++) {
            for(int j = 0; j < i; j++) {
                if(nums[i] > nums[j]) {
                    if(length[i] == length[j] + 1) {
                        count[i] += count[j];
                    } else if(length[i] < length[j] + 1) {
                        count[i] = count[j];
                        length[i] = length[j] + 1;
                    }
                } 
            }
            if(length[i] == max) {
                res += count[i];
            } else if(length[i] > max) {
                max = length[i];
                res = count[i];
            }
        }
        return res;
    }
}
```



### 516. Longest Palindromic Subsequence

找最长回文子序列。依旧是O(N * N), O(N). dp[i\][j]代表从i到j最长回文子序列的长度，对于任意k小于i，如果arr[i] == arr[j], arr[k\][i] = arr[k + 1\][i - 1] + 2. 

```java
class Solution {
    public int longestPalindromeSubseq(String s) {
        if(s == null || s.length() == 0)  return 0;
        char[] arr = s.toCharArray();
        int n = arr.length, res = 1;
        int[][] dp = new int[n][n];
        for(int i = 0; i < n; i++) {
            dp[i][i] = 1;
            for(int j = i - 1; j >= 0; j--) {
                if(arr[i] == arr[j]) {
                    dp[j][i] = (j == i - 1? 2: 2 + dp[j + 1][i - 1]);
                } else {
                    dp[j][i] = Math.max(dp[j + 1][i], dp[j][i - 1]);
                }
                res = Math.max(dp[j][i], res);
            }
        }
        return res;
    }
}
```



### 1027. Longest Arithmetic Sequence

求最长等差序列长度。这种问题要注意不可以用1dDP，因为对于任意一个元素，都可以是多个差不相同的等差数列的一部分。利用Map存不同差的1dDP。O(N * N), O(N * N).

```java
class Solution {
    public int longestArithSeqLength(int[] A) {
        if(A == null || A.length == 0)  return 0;
        int n = A.length, res = 1;
        Map<Integer, int[]> map = new HashMap<>();
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                int diff = A[i] - A[j];
                if(!map.containsKey(diff)) {
                    map.put(diff, new int[n]);
                    Arrays.fill(map.get(diff), 1);
                }
                int[] cur = map.get(diff);
                cur[i] = Math.max(cur[i], cur[j] + 1);
                res = Math.max(cur[i], res);
            }
        }
        return res;
    }
}
```



### 873. Length of Longest Fibonacci Subsequence

dp[i]代表以元素i结尾的最长子序列，同理不可以用dp[i - 1]直接更新。要保证序列的完整性，用Map存每一个元素的index，根据前两个具有斐波那契关系的dp更新。O(N * N), O(N).

```java
class Solution {
    public int lenLongestFibSubseq(int[] A) {
        if(A == null || A.length < 3)  return 0;
        Map<Integer, Integer> map = new HashMap<>();
        int n = A.length;
        for(int i = 0; i < n; i++) map.put(A[i], i);
        int[][] dp = new int[n][n];
        int res = 0;
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                int k = map.getOrDefault(A[i] - A[j], -1);
                dp[j][i] = (k >= 0 && A[k] < A[j])? dp[k][j] + 1: 2;
                res = Math.max(dp[j][i], res);
            }
        }
        return res >= 3? res: 0;
    }
}
```



### 368. Largest Divisible Subset

用另一个数组来记录前面的状态。

```java
class Solution {
    public List<Integer> largestDivisibleSubset(int[] nums) {
        if(nums == null || nums.length == 0)    return new ArrayList<Integer>();
        int n = nums.length;
        int[] dp = new int[n];
        dp[0] = 1;
        Arrays.sort(nums);
        int[] pre = new int[n];
        for(int i = 1; i < n; i++) {
            int max = 1;
            int p = i;
            for(int j = 0; j < i; j++) {
                if(nums[i] % nums[j] == 0) {
                    int cur = dp[j] + 1;
                    if(cur > max) {
                        max = cur;
                        p = j;
                    }
                }
            }
            pre[i] = p;
            dp[i] = max;
        }
        int max = 1, p = 0;
        List<Integer> res = new ArrayList<>();
        for(int i = 0; i < n; i++) {
            if(dp[i] > max) {
                max = dp[i];
                p = i;
            }
        }
        while(true) {
            res.add(0, nums[p]);
            if(pre[p] != p) {
                p = pre[p];
            } else {
                break;
            }
        }
        return res;
    }
}
```