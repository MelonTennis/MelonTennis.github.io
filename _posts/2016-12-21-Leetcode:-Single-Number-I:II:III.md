---
layout: post
title: Leetcode: Single Number I/II/III
subtitle: Single Number I/II/III
date: 2016-12-21
categories: Leetcode
tags: [Hashmap, Bit]
catalog: true
---

### Single Number I

All appear twice except for one, find that one.

Naive O(nlgn) ~~26%?~~:

```java
public class Solution {
    public int singleNumber(int[] nums) {
        Arrays.sort(nums);
        for(int i = 0; i < nums.length-2; i++) {
            if(nums[i]!=nums[i+1] && i%2 == 0) {
                return nums[i];
            }
        }
        
        return nums[nums.length-1];
    }
}
```

Bit Manipulation O(n) ~~40%~~:

> A ^ A = 0 

```java
public class Solution {
    public int singleNumber(int[] nums) {
        int res = 0;
        for(int n: nums){
            res ^= n;
        }
        return res;
    }
}
```

### Single Number II

All appear three times except for one, find that one.

~~…Medium但是我不会~~ 

> [中文](http://blog.csdn.net/jiadebin890724/article/details/23306837)
>
> [English](https://discuss.leetcode.com/topic/11877/detailed-explanation-and-generalization-of-the-bitwise-operation-method-for-single-numbers/3)

嗯，用int的32位存储所有数字每一位上1出现的次数，不断进行模3操作，如果为1则结果中那一位为1。brillient!

```java
public class Solution {
    public int singleNumber(int[] nums) {
        int result = 0;
        for(int i = 0; i < 32; i++) {
            int sum = 0;
            for(int j = 0; j < nums.length; j++) {
                if(((nums[j] >> i) & 1) == 1){
                    sum++;
                    sum %= 3;
                }
            } // for j                
            if(sum != 0) {
                result |= sum << i;
                }
            
        } // for i
        return result;
    }
}
```

Complexity: O(32*n), O(1)

### Single Number III

Two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.

Naive: HashSet O(n), O(n)

```java
public class Solution {
    public int[] singleNumber(int[] nums) {
        HashSet<Integer> set = new HashSet<Integer>();
        for(int i = 0; i < nums.length; i++) {
            if(set.contains(nums[i])) {
                set.remove(nums[i]);
            }else{
                set.add(nums[i]);
            }
        } // for
        Integer[] arr = set.toArray(new Integer[0]);
        int[] a  =  new int[arr.length];
        for(int j = 0; j < arr.length; j++)
            a[j] = arr[j].intValue();
        return a;
    }
}
```

Bit Manipulation: O(n), O(1)

> [Discuss](https://discuss.leetcode.com/topic/21605/accepted-c-java-o-n-time-o-1-space-easy-solution-with-detail-explanations)

首先找到x1 ^ x2 (A ^ A = 0), 其不为0， 找到不为0的位(最后一个1)，对于两个数异或，这一位必然一个为1，一个为0， 遍历原数组进行异或，其他异或结果为0，将两个数分开。

```java
public class Solution {
    public int[] singleNumber(int[] nums) {
        // Pass 1 : 
        // Get the XOR of the two numbers we need to find
        int diff = 0;
        for (int num : nums) {
            diff ^= num;
        }
        // Get its last set bit
        diff &= -diff;
        
        // Pass 2 :
        int[] rets = {0, 0}; // this array stores the two numbers we will return
        for (int num : nums)
        {
            if ((num & diff) == 0) // the bit is not set
            {
                rets[0] ^= num;
            }
            else // the bit is set
            {
                rets[1] ^= num;
            }
        }
        return rets;
    }
}
```

amazing 👆太困了引用自discuss的链接。~~洗洗睡~~ 

