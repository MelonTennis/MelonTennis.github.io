---
layout: post
title: LC216. Word Ladder II
subtitle: 
date: 2016-12-18
categories: Leetcode
tags: [BFS, Array, DFS, Hashmap]
description: LC.126, BFS, DFS, Hashmap
catalog: true

---

### Word Ladder II

#### Question

Given two words (*beginWord* and *endWord*), and a dictionary's word list, find all shortest transformation sequence(s) from *beginWord* to *endWord*, such that:

1. Only one letter can be changed at a time
2. Each intermediate word must exist in the word list

#### Analysis

> [BFS, DFS solution](https://discuss.leetcode.com/topic/27504/my-concise-java-solution-based-on-bfs-and-dfs/2) from Discussion

Main point is to build a hash map whose key is String, and value is List of neighbour Strings and another Hashmap with key of String and value of the distance to the beginString. Using bfs to build the hashmaps and using dfs to find the result. Record the distance so that we can ensure we are finding the shrotest ways, and without cycle. 

#### Code

```java
public class Solution {
    public List<List<String>> findLadders(String beginWord, String endWord, Set<String> wordList) {
        // using bfs to find each neighbour of each word and save in hashmap
        // using dfs to find the path
        // record the neighbours and distance to start word, in case of duplicate or circle
        List<List<String>> res = new ArrayList<List<String>>();
        HashMap<String, ArrayList<String>> neighbour = new HashMap<>();
        HashMap<String, Integer> distance = new HashMap<>();
        ArrayList<String> solution = new ArrayList<String>();
        
        if(wordList.size() == 0)    return res;
        wordList.add(endWord); // add end to wordList
        distance.put(beginWord, 0);
        bfs(beginWord, endWord, wordList, distance, neighbour); // build distance & neighbour
        dfs(beginWord, endWord, wordList, distance, neighbour, solution, res); // dfs search solutions
        return res;
    } // findLadders
    
    public void bfs(String beginWord, String endWord, Set<String> wordList, HashMap<String, Integer> distance, HashMap<String, ArrayList<String>> neighbour){
        for(String word: wordList){
            neighbour.put(word, new ArrayList<String>());
        } // for
        Queue<String> queue = new LinkedList<String>();
        distance.put(beginWord, 0);
        queue.add(beginWord);
        while(!queue.isEmpty()){
            int  count = queue.size();
            boolean findEnd = false;
            for(int i = 0; i < count; i++){
                String cur = queue.poll();
                int curD = distance.get(cur);
                ArrayList<String> nei = getNeighbour(cur, wordList);
                for(String n: nei){
                    neighbour.get(cur).add(n);
                    if(!distance.containsKey(n)) { 
                        distance.put(n, curD+1); // least distance
                        if(endWord.equals(n)){
                            findEnd = true;
                        }else{
                            queue.offer(n);
                        }
                    } // if first visit
                } // for n
                if(findEnd){
                    break;
                }
            } // for
        } // while
    } // bfs
    
    // find all neighbour of one word
    public ArrayList<String> getNeighbour(String s, Set<String> wordList){
        ArrayList<String> res = new ArrayList<>();
        char[] arr = s.toCharArray();
        for(char c = 'a'; c <= 'z'; c++){
            for(int i = 0; i < arr.length; i++){
                if(arr[i] == c) continue;
                char oldc = arr[i];
                arr[i] = c;
                if(wordList.contains(new String(arr))){
                    res.add(new String(arr));
                }
                arr[i] = oldc;
            }
        } // for c
        return res;
    }
    
    public void dfs(String cur, String endWord, Set<String> wordList, HashMap<String, Integer> distance, HashMap<String, ArrayList<String>> neighbour, ArrayList<String> solution, List<List<String>> res){
        solution.add(cur);
        if(cur.equals(endWord)){ 
            res.add(new ArrayList<String>(solution)); // copy of current solution
        }else{
            for(String next: neighbour.get(cur)){
                if(distance.get(next) == distance.get(cur)+1){
                    dfs(next, endWord, wordList, distance, neighbour, solution, res);
                }
            }
        }
        solution.remove(solution.size() - 1); // backtracking
    }
}
```

 ⚠️： solution加入res一定要新建ArrayList，Backtracking的恢复。