---
layout: post
title: Leetcode - Ugly Number
subtitle: LC263, LC264, LC313
date: 2019-08-25
categories: Leetcode
tags: [DP, Heap]
description: 
catalog: true
---

## Ugly Number

Leetcode 的 ugly number，涉及到math，dp，heap。

### Ugly Number

判断是不是ugly number，定义为质数因子只包括2，3，5的正数。可以迭代，可以递归。

```java
public class Solution {
    public boolean isUgly(int num) {
        num = test(num);
        if(num == 1)
            return true;
        return false;
    }
    private int test(int n)
    {
        if((n%2 == 0 || n%3 == 0 || n%5 == 0) && n>=2) 
        {
        if(n%2 == 0 && n>=2)
            n = n/2;
        if(n%3 == 0 && n>=3)
            n = n/3;
        if(n%5 == 0 && n>=5)
            n = n/5;
        n = test(n);
        }
        return n;
    }
}
```



### Ugly Number II

找到第n个ugly number，第一个是1。DP问题，依次更新2，3，5的因子数量即可。O(N), O(N).

```java
 class Solution {
    public int nthUglyNumber(int n) {
        int idx2 = 0, idx3 = 0, idx5 = 0;
        int[] ugly = new int[n];
        ugly[0] = 1;
        for(int i = 1; i < n; i++){
            int cur2 = ugly[idx2]*2;
            int cur3 = ugly[idx3]*3;
            int cur5 = ugly[idx5]*5;
            int res = Math.min(Math.min(cur2, cur3), cur5);
            if(res == cur2){
                idx2++;
            }
            if(res == cur3){
                idx3++;
            }
            if(res == cur5){
                idx5++;
            }
            ugly[i] = res; 
        }
        return ugly[n-1];
    }
}
```



### Super Ugly Number

在II的基础上，把2，3，5换成一个大小为k的数组。暴力改成O(NK), O(N).

```java
public class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int length = primes.length;
        int[] index = new int[length];
        int[] ugly = new int[n+1];
        ugly[0] = 1;
        for(int i = 1; i <= n; i++){
            ugly[i] = Integer.MAX_VALUE;
        }
        for(int i = 1;i < n;i++){
            for(int j = 0; j < length; j++) {
                ugly[i] = Math.min(ugly[i], primes[j]*ugly[index[j]]);
             }
            for(int j = 0; j < length; j++) {
                if(ugly[i] == primes[j]*ugly[index[j]]){
                    index[j]++;
                }
            }
        }
        return ugly[n-1];
    }
}
```