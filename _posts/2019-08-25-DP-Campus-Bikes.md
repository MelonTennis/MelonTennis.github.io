---
layout: post
title: Leetcode - Word Break
subtitle: LC139, LC140, LC343
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## Word Break

### Word Break - Medium

只要判断就可以，因此存boolean[]，O(N * N), O(N).

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        // dp
        Set<String> dict = new HashSet<>(wordDict);
        int n = s.length();
        boolean[] dp = new boolean[n + 1];
        dp[0] = true;
        for(int i = 1; i <= n; i++) {
            for(int j = i + 1; j <= n + 1; j++) {
                if(dict.contains(s.substring(i - 1, j - 1)) && dp[i - 1]) {
                    dp[j - 1] = true;
                }
            }
        }
        return dp[n];
    }
}
```



### Word Break II - Hard

求所有可能的word break结果。在背包问题里讲了可以开一个记录之前路径的数组，这里我用dfs + memo。O(N^3). 

> Size of recursion tree can go up to n * n. The creation of list takes *n* time.

```java
class Solution {
    Map<String, List<String>> map = new HashMap<>();
    public List<String> wordBreak(String s, List<String> wordDict) {
        // dfs with memo
        if(s == null || wordDict == null){
            return new ArrayList<String>();
        }
        List<String> res = dfs(s, wordDict);
        return res;
    }
    
    public List<String> dfs(String s, List<String> wordDict){
        if(map.containsKey(s)){
            return map.get(s);
        }
        List<String> res = new ArrayList<>();
        if(s.equals("")){
            res.add("");
            return res;
        }
        for(String word: wordDict){
            if(word.length() > s.length())  continue;
            if(s.substring(0, word.length()).equals(word)){
                List<String> next = dfs(s.substring(word.length()), wordDict);
                for(String str: next){
                    if(!str.equals(""))
                        res.add(word + " " + str);
                    else
                        res.add(word + str);
                }
            }
        }
        map.put(s, res);
        return res;
    }
}
```

