---
layout: post
title: LC471. Encode String with Shortest Length
subtitle: Encode the string such that its encoded length is the shortest.
date: 2017-01-02
categories: Leetcode
tags: [DP, KMP, String]
catalog: true
---

###  LC471. Encode String with Shortest Length

Given a **non-empty** string, encode the string such that its encoded length is the shortest. The encoding rule is: `k[encoded_string]`, where the *encoded_string* inside the square brackets is being repeated exactly *k* times.

**Example:**

```
Input: "abbbabbbcabbbabbbc"
Output: "2[2[abbb]c]"
Explanation: "abbbabbbc" occurs twice, but "abbbabbbc" can also be encoded to "2[abbb]c", so one answer can be "2[2[abbb]c]".

```

> [DP solution](https://discuss.leetcode.com/topic/71963/accepted-solution-in-java)

DP, 这个字符串编码的可能性有很多种，所以DP的方式就是对于每一种可能都求出一个结果最后得到最短的那个。

DP{i, j}代表编码后的s.substring(i, j+1). DP的过程中要注意每一个子串都具有编码和不编码的可能性，对于substring的重复情况，重复的部分也可能被编码。

递推式：DP{i, j} = arg minLength(s.substring(i, j), DP{i, k}+DP{k+1, j}, after coding repeat.s.substring})

```java
public class Solution {
    public String encode(String s) {
        // dp solution, for each substring of s, check whether it can be encode
        // dp[i][j] means substing of [i, j] after encode
        if(s == null || s.length() <= 4)  return s;
        int len = s.length();
        String[][] dp = new String[len][len];
        for(int sublen = 0; sublen < len; sublen++){
            for(int begin = 0; begin < len - sublen; begin++){
                int end = begin + sublen;
                String sub = s.substring(begin, end + 1);
                dp[begin][end] = sub;
                if(sublen < 4)  continue; // sub.length = sublen + 1
                // get shortest substring combination
                for(int i = begin; i < end; i++){
                    if(dp[begin][i].length() + dp[i+1][end].length() < dp[begin][end].length())
                        dp[begin][end] = dp[begin][i] + dp[i+1][end];
                }
                // if repeat, using kmp to encode then compare with shortest combination
                String pattern = kmp(sub);
                //System.out.println(pattern);
                // no repeat, keep origional -> shorest combination 
                if(pattern.length() == sub.length()) continue;
                // find repeat, repeat should also be encoded
                String encode = sub.length()/pattern.length() + "[" + dp[begin][begin+pattern.length()-1] + "]";
                if(encode.length() < dp[begin][end].length())   dp[begin][end] = encode;
            } // for begin 
        } // for sublen
        return dp[0][len - 1];
    } // code
    
    // kmp method to find repeat pattern in one String
    private String kmp(String str){
        int len = str.length();
        int[] next = new int[len];
        char[] arr = str.toCharArray();
        Arrays.fill(next, -1);
        int i = 0, j = 1;
        for(; j < len; j++){
            i = next[j - 1];
            while(arr[i + 1] != arr[j] && i >= 0) i = next[i];
            if(arr[i + 1] == arr[j]) next[j] = i + 1;
            else next[j] = -1;
        }
        //System.out.println(Arrays.toString(next));
        // if repeat, it must before the 0
        int patternLen = len - next[len - 1] - 1;
        if(patternLen == 0)    return str;
        if(patternLen != len && len % patternLen == 0) {
            return str.substring(0, patternLen);
        } else {
            return str;
        }
    } // kmp
}
```

利用KMP算法来寻找和判断是否存在重复的子串是比较快的。顺便这是LC459题。

O(n^3), 三个循环，每个循环最差n。

#### LC459. Repeated Substring Pattern

Given a non-empty string check if it can be constructed by taking a substring of it and appending multiple copies of the substring together. 

#####  Substring solution:

```java
public class Solution {
    public boolean repeatedSubstringPattern(String str) {
        // each divider of the length, check for the whole string
        if(str == null || str.length() == 0)    return false;
        int len = str.length();
        for(int i = len/2; i >= 1; i--){
            if(len % i != 0)    continue;
            int num = len/i;
            String s = str.substring(0, i);
            for(int j = 1; j < num; j++){
                String sub = str.substring(j*i, i*(j+1));
                if(!s.equals(sub))  break;
                if(j == num-1)  return true;
            }
        }
        return false;
    }
}
```

O(n^2) worst

##### KMP solution:

```java
public class Solution {
    public boolean repeatedSubstringPattern(String str) {
        // kmp?
        if(str == null || str.length() == 0)    return false;
        int len = str.length();
        int[] next = new int[len];
        Arrays.fill(next, -1);
        char[] arr = str.toCharArray();
        int i = 0, j = 1;
        for(; j < len; j++){
            i = next[j-1];
            while(arr[i+1] != arr[j] && i >= 0)   i = next[i];
            if(arr[i+1] == arr[j])  next[j] = i + 1;
            else next[j] = -1;
        } // for j
       // System.out.println(Arrays.toString(next));
        int sublen = len - next[len - 1] - 1;
    //    System.out.println(sublen);
        if(sublen <= 0 || len % sublen != 0 || sublen == len)    return false;
        i = 0; 
        j = len - sublen;
        for(; i < len/sublen; i++){
            for(int k = 0; k < sublen; k++){
                if(arr[i*sublen+k] != arr[j+k])    return false;
            }
            j = len - sublen;
        }
        return true;
    }
}
```

O(n^2) worst  也许我不该一位一位的去比... 竟然更慢😳

#####Rotated string solution:

这题有个巧妙解法是[这样的](<https://leetcode.com/problems/repeated-substring-pattern/discuss/94334/Easy-python-solution-with-explaination>)，复杂度也是O(n^2) worst

> Consider a string `S="helloworld"`. Now, given another string `T="lloworldhe"`, can we figure out if `T` is a rotated version of `S`? Yes, we can! We check if `S` is a substring of `T+T`.
>
> Fine. How do we apply that to this problem? We consider every rotation of string `S` such that it's rotated by `k` units `[k < len(S)]` to the left. Specifically, we're looking at strings "`elloworldh", "lloworldhe", "loworldhel", etc...`
>
> If we have a string that is periodic (i.e. is made up of strings that are the same and repeat `R` times), then we can check if the string is equal to some rotation of itself, and if it is, then we know that the string is periodic. Checking if `S` is a sub-string of `(S+S)[1:-1]` basically checks if the string is present in a rotation of itself for all values of `R` such that `0 < R < len(S)`.

```java
class Solution {
    public boolean repeatedSubstringPattern(String s) {
        String S = (s + s).substring(1, s.length() * 2 - 1);
        return S.contains(s);
    }
}
```

