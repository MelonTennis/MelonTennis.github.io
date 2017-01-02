---
layout: post
title: Leetcode - Word Square
subtitle: LC422, LC425
date: 2017-01-02
categories: Leetcode
tags: [Trie, Backtracking]
catalog: true
---

### LC422. Valid Word Square

新年第一po，发现每次一到法定假日我就什么都不发，感觉错过了很多记录美好的瞬间~~(快够)~~ 顺便这个markdown为什么每次标号都不正确？感觉我好像没错呀。

Anyway， 新年快乐❤️ 爱CS爱Coding我爱这个世界 -> ~~做不出来hard题的自我安慰~~

Given a sequence of words, check whether it forms a valid word square. A sequence of words forms a valid word square if the *k*th row and column read the exact same string, where 0 ≤ *k* < max(numRows, numColumns).

LC422是一个easy题，只要判断所给的***List <String>***是否能构成word square即可。因此思路也是平铺直叙的，主要是要考虑构成word square的字符串长度递减的问题。对每一行的字符串进行扫描，对每一列的构成的子字符进行判断。

```java
public class Solution {
    public boolean validWordSquare(List<String> words) {
         if(words == null || words.size() <= 0) return true;
         int size = words.size();
         int row = 0, col = 0;
         while(row < size){
             String cur = words.get(row);
             // col >= cur.length
             if(col >= cur.length()){
                 for(int m = row; m < size; m++){
                    // System.out.println(col);
                     if(words.get(m).length() > col)   return false;
                 }
                 return true;
             }
             // col < cur.length
             StringBuilder sb = new StringBuilder();
             for(int k = row; k < size; k++){
                 String temp = words.get(k);
                 if(col < temp.length())  sb.append(temp.charAt(col));
                 else break;
             }
             if(!sb.toString().equals(cur.substring(col)))   return false;
             row++;
             col++;
         }
         return true;
    }
}
```

O(mn), O(m). n = words.size(), m = average length of each word

另外StringBuilder的速度优势很明显啊。

### LC425 Word Squares

Given a set of words **(without duplicates)**, find all [word squares](https://en.wikipedia.org/wiki/Word_square) you can build from them.

LC422是判断能否构成word square，LC425是要给出所有的构成可能性，感觉上是回溯／DP或者。。。~~手撕422??~~ String构成的问题利用Trie来解是非常方便的。👇

> [Java with Trie](https://discuss.leetcode.com/topic/63516/explained-my-java-solution-using-trie-126ms-16-16)
>
> [Trie + DFS](https://discuss.leetcode.com/topic/64770/java-dfs-trie-54-ms-98-so-far)

首先对于所有的word构造前缀树，构造一个和行数相同的Node[ ], 利用前缀树搜索word并填满矩阵。这个过程中需要注意回溯，row和col相同或不同时要分情况讨论。

```java
public class Solution {
    class Node{
        String word;
        Node[] nodes;
        Node(){
            word = null;
            nodes = new Node[26];
        }
    } // Node
    
    void add(Node root, String word){
        Node node = root;
        for(char c : word.toCharArray()){
            int index = c - 'a';
            if(node.nodes[index] == null){
                node.nodes[index] = new Node();
            }
            node = node.nodes[index];
        }
        node.word = word;
    } // add
    
    void helper(int row, int col, int len, List<List<String>> res, Node[] rows){
        // add to res
        if(col == row && col == len){
            List<String> cur= new ArrayList<String>();
            for(Node r : rows){
                cur.add(new String(r.word));
            }
            res.add(cur);
        }else{
            // left to right
            if(col < len){
                Node pre_col = rows[col];
                Node pre_row = rows[row];
                for(int i = 0; i < 26; i++){
                    if(rows[col].nodes[i] != null && rows[row].nodes[i] != null){
                            rows[row] = rows[row].nodes[i];
                            if (col != row) rows[col] = rows[col].nodes[i];
                            helper(row, col+1, len, res, rows);
                            rows[row] = pre_row;
                            rows[col] = pre_col;
                    }
                }
            }else{
                helper(row+1, row+1, len, res, rows);
            } // col == row
        } // dfs
    } // helper
    
    public List<List<String>> wordSquares(String[] words) {
        List<List<String>> res = new ArrayList<>();
        if(words == null || words.length == 0)    return res;
        int len = words[0].length();
        Node root = new Node();
        for(int i = 0; i < words.length; i++)   add(root, words[i]);
        Node[] rows = new Node[len];
        for(int i = 0; i < len; i++)    rows[i] = root;
        helper(0, 0, len, res, rows);
        return res;
    } // wordSquares
}
```

一个月前做的都不记得了。O(26*n^2) 

