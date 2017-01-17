---
layout: post
title: Leetcode - Arithmetic Slices
subtitle: LC413, LC446
date: 2017-01-15
categories: Leetcode
tags: [DP]
catalog: true
---

#### LC413. Arithmetic Slices

A sequence of number is called arithmetic if it consists of at least three elements and if the difference between any two consecutive elements is the same.

For example, these are arithmetic sequence:

```
1, 3, 5, 7, 9
7, 7, 7, 7
3, -1, -5, -9
```

The following sequence is not arithmetic.

```
1, 1, 2, 5, 7
```

A zero-indexed array A consisting of N numbers is given. A slice of that array is any pair of integers (P, Q) such that 0 <= P < Q < N.

A slice (P, Q) of array A is called arithmetic if the sequence:
A[P], A[p + 1], ..., A[Q - 1], A[Q] is arithmetic. In particular, this means that P + 1 < Q.

The function should return the number of arithmetic slices in the array A.

**Example:**

```
A = [1, 2, 3, 4]

return: 3, for 3 arithmetic slices in A: [1, 2, 3], [2, 3, 4] and [1, 2, 3, 4] itself.

```

想要求arrayA中长度大于2的连续递增子序列，利用带有记忆的DP就可以。memo[i]代表以i为结尾的连续递增子序列有多少，结果就是sum(memo).

```java
public class Solution {
    public int numberOfArithmeticSlices(int[] A) {
        int length = A.length;
        if(length < 3) {
            return 0;
        }
        int[] memo = new int[length];
        for(int i = 2; i < length; i++) {
            memo[i] = A[i]-A[i-1]==A[i-1]-A[i-2]?memo[i-1]+1:0;
        }
        int res = 0;
        for(int i = 0; i < length; i++) {
            res += memo[i];
        }
        return res;
    }
}
```

O(n), O(n) 我猜想这个可以用O(1) space。

#### LC446. Arithmetic Slices II - Subsequence

A sequence of numbers is called arithmetic if it consists of at least three elements and if the difference between any two consecutive elements is the same.

For example, these are arithmetic sequences:

```
1, 3, 5, 7, 9
7, 7, 7, 7
3, -1, -5, -9
```

The following sequence is not arithmetic.

```
1, 1, 2, 5, 7
```

A zero-indexed array A consisting of N numbers is given. A **subsequence** slice of that array is any sequence of integers (P0, P1, ..., Pk) such that 0 ≤ P0 < P1 < ... < Pk < N.

A **subsequence** slice (P0, P1, ..., Pk) of array A is called arithmetic if the sequence A[P0], A[P1], ..., A[Pk-1], A[Pk] is arithmetic. In particular, this means that k ≥ 2.

The function should return the number of arithmetic subsequence slices in the array A.

The input contains N integers. Every integer is in the range of -231 and 231-1 and 0 ≤ N ≤ 1000. The output is guaranteed to be less than 231-1.

**Example:**

```
Input: [2, 4, 6, 8, 10]

Output: 7

Explanation:
All arithmetic subsequence slices are:
[2,4,6]
[4,6,8]
[6,8,10]
[2,4,6,8]
[4,6,8,10]
[2,4,6,8,10]
[2,6,10]
```

如果不是连续的子序列而是可以有间隔的序列，问题就复杂了好多哦 ->看看discuss里大神的解答

> [Java solution](https://discuss.leetcode.com/topic/67012/java-15-lines-solution)

> The idea is to store the diff and number of times this diff has appeared before in a hash map for each element;
> And we only calculate the diff between current element and the element before current.
> for example:
> [2] stores a empty hashmap
> [2,4] now 4 stores a mapentry [2,1] because 4-2 = 2,and for element 2 stores nothing
> [2,4,6] 6-4 =2 and since element 4 stores[2,1], which means diff 2 has appeared once before, so count = 1 and we put [2,2] in 6. Also 6-2 = 4, we put [4,1] in 6;

对于A中的每个元素，计算它之前多有元素的差并存入map，map中存差值，不包括当前元素对于这个差值的等差数列的长度。每次更新这个值，如果在之前的元素中也存在这个差值的等差数列，就更新res。

```java
public class Solution {
    public int numberOfArithmeticSlices(int[] A) {
        int re = 0;
        HashMap<Integer, Integer>[] maps = new HashMap[A.length];
        for(int i=0; i<A.length; i++) {
            maps[i] = new HashMap<>();
            int num = A[i];
            for(int j=0; j<i; j++) {
                if((long)num-A[j]>Integer.MAX_VALUE) continue;
                if((long)num-A[j]<Integer.MIN_VALUE) continue;
                int diff = num - A[j];
                int count = maps[j].getOrDefault(diff, 0);
                maps[i].put(diff, maps[i].getOrDefault(diff,0)+count+1);
                re += count;
            }
        }
        return re;
    }
}
```

O(n^2), O(n)

试试公式

$$a^2 + b^2 = c^2$$

$$x_\mu$$