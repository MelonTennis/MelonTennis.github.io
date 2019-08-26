---
layout: post
title: Leetcode - Longest Subsequence
subtitle: LC300, LC376, LC673, LC516, LC1027, LC873, LC368, LC842, LC960
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



### 376. Wiggle Subsequence

```java
class Solution {
    public int wiggleMaxLength(int[] nums) {
        if(nums.length < 2) return nums.length;
        int down = 1, up = 1;
        for(int i = 1; i < nums.length; i++) {
            if(nums[i] > nums[i - 1])   up = down + 1;
            if(nums[i] < nums[i - 1])   down = up + 1;
        }
        return Math.max(up, down);
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



### 842. Split Array into Fibonacci Sequence

同样都是找斐波那契，这题dfs找到一个有效结果。O(N!)

```java
class Solution {
    public List<Integer> splitIntoFibonacci(String S) {
        List<Integer> res = new ArrayList<>();
        if(dfs(S, res, 0))  return res;
        return new ArrayList<Integer>();
    }
    
    private boolean dfs(String S, List<Integer> res, int start) {
        if(S == null)   return false;
        int len = S.length();
        if(start == len && res.size() >= 3) {
            return true;
        }
        if(start >= len)    return false;
        for(int i = start; i < len; i++) {
            if(i > start && S.charAt(start) == '0') break;
            long num = Long.valueOf(S.substring(start, i + 1));
            if(num > Integer.MAX_VALUE) break;
            if(res.size() < 2) {
                res.add((int)num);
                if(dfs(S, res, i + 1))  return true;
                res.remove(res.size() - 1);
            } else {
                int cur = (int)num;
                int sz = res.size();
                int pre_sum = res.get(sz - 1) + res.get(sz - 2);
                if(cur < pre_sum)   continue;
                if(cur > pre_sum)   break;
                res.add(cur);
                if(dfs(S, res, i + 1))  return true;
                res.remove(res.size() - 1);
            }
        }
        return false;
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



### LC960. Delete Columns to Make Sorted III - Hard

这题求最少删去多少列能够让数组A里面的字符串全部符合字母递增排序。一开始陷入了迷惑，看到了[Lee215](<https://leetcode.com/problems/delete-columns-to-make-sorted-iii/discuss/205679/C%2B%2BJavaPython-Maximum-Increasing-Subsequence>)的解答才会做。这里把最少删去多少列转换为最多保留多少列就好写状态转换方程了。难怪事hard题。dp[i]表示对于每一个字符串a，以i为结尾的递增subsequence最长长度。O(N * N * K), O(N).

```java
class Solution {
    public int minDeletionSize(String[] A) {
        // convert to longest increase subsequence
        // O(n * n * k)
        int n = A[0].length();
        int k = A.length;
        // means max length of increase subsequence stop at index i
        // for each str in A
        // dp[i] = max(dp[i], dp[j] + 1)
        int[] dp = new int[n];
        Arrays.fill(dp, 1);
        int res = n - 1;
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                int m = 0;
                for(m = 0; m < k; m++) {
                    if(A[m].charAt(i) < A[m].charAt(j)) {
                        break;
                    }
                }
                if(m == k && dp[i] < dp[j] + 1) {
                    dp[i] = dp[j] + 1;
                }
            }
            res = Math.min(res, n - dp[i]);
        }
        return res;
    }
}
```



这一题是[LC 955 Delete Columns to Make Sorted II](<https://leetcode.com/problems/delete-columns-to-make-sorted-ii/>) 的followup。LC955是一个greedy问题，[这篇博客](<https://blog.csdn.net/creat2012/article/details/40581081>)讲的很好， 贪心问题正确的条件是每一步的最优解都包含上一步的最优解。LC960局部最优解不一定包含上一步的最优解，因此要用DP来解决。

