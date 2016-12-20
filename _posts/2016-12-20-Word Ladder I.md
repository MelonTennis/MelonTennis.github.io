---

layout: post
title: LC127. Word Ladder
subtitle: Word Ladder I - one of solution
date: 2016-12-20
categories: blog
tags: [LC, BFS]
description: LC.127 Word Ladder I - BFS
catalog: true

---

### Word Ladder I

先做了[Word Ladder II](https://yijiajin.github.io/blog/2016/12/18/Word-Ladder-II/), 很 naive的以为是一种做法。~~当然不是了~~

#### Question

Given two words (*beginWord* and *endWord*), and a dictionary's word list, find the length of shortest transformation sequence from *beginWord* to *endWord*, such that:

1. Only one letter can be changed at a time
2. Each intermediate word must exist in the word list

#### Analysis

> [Two-ends BFS in Java](https://discuss.leetcode.com/topic/29303/two-end-bfs-in-java-31ms)

Naive thought is using BFS to search for the solution, but by using two-ends BFS to search, each time always searching from the smaller graph, the running time can be reduced significantly. 

> "The idea behind bidirectional search is to run two simultaneous searches one forward from the initial state and the other backward from the goal hoping that the two searches meet in the middle. The motivation is that b^(d/2) + b^(d/2) is much less than b^d. b is branch factor, d is depth. "
>
> ----- section 3.4.6 in Artificial Intelligence - A modern approach by Stuart Russel and Peter Norvig

#### Code

```java
public class Solution {
    public int ladderLength(String beginWord, String endWord, Set<String> wordList) {
        // bfs find each solution and save the shortest one -> naive
        // building two set and renew them level by level, always search from the smaller one to the larger one
        // use two-end BFS, reduce the searching time
        Set<String> front = new HashSet<String>();
        Set<String> end = new HashSet<String>();
        int len = 1;
        HashSet<String> visit = new HashSet<String>();
        front.add(beginWord);
        end.add(endWord);
        // two set both not empty
        while(!front.isEmpty() && !end.isEmpty()){
            // search from the smaller set
            if(front.size() > end.size()){
                Set<String> temp = end;
                end = front;
                front = temp;
            }
            // current level of String
            Set<String> cur = new HashSet<String>();
            for(String word: front){
                char[] arr = word.toCharArray();
                for(int i = 0; i < arr.length; i++){
                    for(char c = 'a'; c <= 'z'; c++){ // neighbour
                        char oldc = arr[i];
                        if(oldc == c)   continue;
                        arr[i] = c; 
                        String str = new String(arr);
                        if(end.contains(str)){ // find
                            return len + 1;
                        }
                        if(!visit.contains(str) && wordList.contains(str)){
                            visit.add(str);
                            cur.add(str);
                        }
                        arr[i] = oldc;
                    } // for c
                } // for i
            } // for word
            front = cur; // next level
            len++;
        } // while
        return 0; // not found path
    }
}
```

