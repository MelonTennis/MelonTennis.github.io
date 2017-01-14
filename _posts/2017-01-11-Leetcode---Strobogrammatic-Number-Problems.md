---
layout: post
title: Leetcode - Strobogrammatic Number
subtitle: LC246, LC247, LC248
date: 2017-01-11
categories: Leetcode
tags: [Backtracking]
catalog: true
---

#### LC246. Strobogrammatic Number

A strobogrammatic number is a number that looks the same when rotated 180 degrees (looked at upside down).

Write a function to determine if a number is strobogrammatic. The number is represented as a string.

For example, the numbers "69", "88", and "818" are all strobogrammatic.

```java
public class Solution {
    public boolean isStrobogrammatic(String num) {
        int len = num.length();
        for(int i = 0; i < len; i++){
            if(num.charAt(i) == '2' || num.charAt(i) == '3' || num.charAt(i) == '4' || num.charAt(i) == '5' || num.charAt(i) == '7' || (num.charAt(i) != num.charAt(len-i-1) && num.charAt(i) == '8') || (num.charAt(i) == '6' && num.charAt(len-i-1) != '9' ) || (num.charAt(i) == '9' && num.charAt(len-i-1) != '6' ) || (num.charAt(i) != num.charAt(len-i-1) && num.charAt(i) == '1') ){
                return false;
            } // if
        } // for
        return true;
    }
}
```

O(n), naive solution :)

#### LC247. Strobogrammatic Number II

A strobogrammatic number is a number that looks the same when rotated 180 degrees (looked at upside down).

Find all strobogrammatic numbers that are of length = n.

For example,
Given n = 2, return `["11","69","88","96"]`.

输出所有n位的中心对称数。分成n为奇数／偶数情况。

```java
public class Solution {
    public List<String> findStrobogrammatic(int n) {
        List<String> res, cur;
        res = ((n&1)==1)?new ArrayList<String>(Arrays.asList("0", "1", "8")):new ArrayList<String>(Arrays.asList(""));
        if(n < 2)  return res;
        for(int i = n; i > 1; i-=2){
            cur = res;
            res = new ArrayList<String>();
            for(String str: cur){
                if(i > 3)  res.add("0" + str + "0"); // 0 should not be the beginning number
                res.add("1" + str + "1");
                res.add("6" + str + "9");
                res.add("9" + str + "6");
                res.add("8" + str + "8");
            } // for str
        } // for i
        return res;
    }
}
```

O(n)

#### LC248. Strobogrammatic Number III

A strobogrammatic number is a number that looks the same when rotated 180 degrees (looked at upside down).

Write a function to count the total strobogrammatic numbers that exist in the range of low <= num <= high.

For example,
Given low = "50", high = "100", return 3. Because 69, 88, and 96 are three strobogrammatic numbers.

输出low与high之间的中心对称数的String.

> [java solution](https://discuss.leetcode.com/topic/31386/concise-java-solution)



```java
public class Solution {
    int count = 0;
    char[][] pairs = {{'0', '0'}, {'1', '1'}, {'6', '9'}, {'9', '6'}, {'8', '8'}};
    public int strobogrammaticInRange(String low, String high) {
        for(int len = low.length(); len <= high.length(); len++){
            char[] cur = new char[len];
            search(cur, low, high, 0, len - 1);
        }
        return count;
    }
    
    private void search(char[] cur, String low, String high, int left, int right){
        if(left > right){
            String s = new String(cur);
            if(s.length() == low.length() && s.compareTo(low) < 0)  return;
            if(s.length() == high.length() && s.compareTo(high) > 0)    return;
            count++;
            return;
        }
        for(char[] pair: pairs){
            cur[left] = pair[0];
            cur[right] = pair[1];
            if(cur[0] == '0' && cur.length != 1) continue;
            if(left == right && pair[0] != pair[1]) continue;
            search(cur, low, high, left+1, right-1);
        }   
    } // search
}
```

O(n), O(n)