---
layout: post
title: Leetcode - Trie Medium
subtitle: LC421, LC208, LC211
date: 2016-12-27
categories: Leetcode
tags: [Trie]
catalog: true
---

Trie, 前缀树。个人习惯每个Node具有如下结构：

```java
class Node{
  String word;
  Node[] next;
  public Node(){
    word = null;
    next = new Node[26];
  }
}
```

前缀树对于String的一些问题可以有效的减少时间复杂度。

### LC208. Implement Trie

Implement a trie with `insert`, `search`, and `startsWith` methods.

前缀树入门题，抄起Trie写。

```java
class TrieNode {
    // Initialize your data structure here.
    String word;
    TrieNode[] next;
    public TrieNode() {
        word = null;
        next = new TrieNode[26];
    }
}

public class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    // Inserts a word into the trie.
    public void insert(String word) {
        TrieNode r = root;
        for(char c: word.toCharArray()){
            if(r.next[c-'a'] == null){
                r.next[c-'a'] = new TrieNode();
            }
            r = r.next[c-'a'];
        }
        r.word = word;
    }

    // Returns if the word is in the trie.
    public boolean search(String word) {
        TrieNode r = root;
        for(char c: word.toCharArray()){
            if(r.next[c-'a'] == null){
                return false;
            }
            r = r.next[c-'a'];
        }
        return r.word != null;
    }

    // Returns if there is any word in the trie
    // that starts with the given prefix.
    public boolean startsWith(String prefix) {
        TrieNode r = root;
        for(char c: prefix.toCharArray()){
            if(r.next[c-'a'] == null)   return false;
            r = r.next[c-'a'];
        }
        return true;
    }
}

// Your Trie object will be instantiated and called as such:
// Trie trie = new Trie();
// trie.insert("somestring");
// trie.search("key");
```

前缀树的实现最近写了几个题就比较熟悉了。

### LC421. Maximum XOR of Two Numbers in an Array

Given a **non-empty** array of numbers, a0, a1, a2, … , an-1, where 0 ≤ ai < 2^31. Find the maximum result of ai XOR aj, where 0 ≤ *i*, *j* < *n*. In O(*n*) runtime. 

Analysis:  

> [Discuss - bit manipulation](https://discuss.leetcode.com/topic/63213/java-o-n-solution-using-bit-manipulation-and-hashmap/2)

要求O(n), naive的做法是O(n^2).  ai < 2^31，一定在暗示位运算。

* Bit manipulation

想法是算出每一位的结果最后得到最终的结果，O(32n). 对于i属于 [0, 32)从左到右的每一位，首先找到每个num的i个前缀bit，首先假设max = 0，对于所有的前缀求出最大的可能性并保存到max中不断更新。

```java
public class Solution {
    public int findMaximumXOR(int[] nums) {
        int max = 0, mask = 0;
        for(int i = 31; i >= 0; i--){
            mask |= (1<<i);
            HashSet<Integer> set = new HashSet<Integer>();
            for(int num: nums){
                set.add(num&mask);
            }
            int temp = max | (1<<i);
            for(int prefix:set){
                if(set.contains(temp ^ prefix)){
                    max = temp;
                }
            }
        }
        return max;
    }
}
```

* Trie

说好的前缀树tag呢 -> 将每个num加入二进制形式的trie，寻找最大的异或值，即对于每个num的反码是否存在。这个写法由于是O(60n)而139ms。还是discuss厉害。

> [Discuss: Trie, O(30n)](https://discuss.leetcode.com/topic/64753/31ms-o-n-java-solution-using-trie/2)

```java
public class Solution {
    class Node{
        Node[] next;
        Integer num;
        public Node nextNode(int bit){
            if(next[bit] == null)   next[bit] = new Node();
            return next[bit];
        }
        public Node(){
            next = new Node[2];
            num = null;
        }
    }
    
    public int findMaximumXOR(int[] nums) {
        Node root = new Node();
        int max = 0;
        // build trie
        for(int num: nums){
            Node node = root;
            for(int i = 30; i >= 0; i--){
                int temp = (num >> i) & 1;
                if(node.next[temp] == null) node.next[temp] = new Node();
                node = node.next[temp];
            }
            node.num = num;
        }
        // find
        for(int num: nums){
            Node node = root;
            int maxtemp = 0;
            for(int i = 30; i >= 0; i--){
                int temp = (num >> i) & 1;
                if(node.next[1 ^ temp] != null){
                    node = node.next[1 ^ temp];
                    maxtemp |= (1 << i);
                }else{
                    node = node.next[temp];
                }
            } // for i
            max = Math.max(max, maxtemp);
        } // for num
        return max;
    }
}
```


### LC211. Add and Search Word - Data structure design

Design a data structure that supports the following two operations:

```
void addWord(word)
bool search(word)

```

search(word) can search a literal word or a regular expression string containing only letters `a-z` or `.`. A `.` means it can represent any one letter.

Analysis：

前缀树 + 正则，前缀树的实现没有区别，搜索的时候区分char情况即可。

```java
public class WordDictionary {
    // typical Trie 
    class Node{
        String word;
        Node[] next;
        public Node(){
            word = null;
            next = new Node[26];
        }
    }
    Node root = new Node();
    // Adds a word into the data structure.
    public void addWord(String word) {
        if(word == null)    return;
        Node node = root;
        char[] arr = word.toCharArray();
        for(char c: arr){
            if(node.next[c-'a'] == null){
                node.next[c-'a'] = new Node();
            }
            node = node.next[c-'a'];
        } // for
        node.word = word;
    }

    // Returns if the word is in the data structure. A word could
    // contain the dot character '.' to represent any one letter.
    public boolean search(String word) {
        return helper(word, root, 0);
    }
    
    // backtrack searching word
    public boolean helper(String word, Node node, int pos){
        if(node == null || word == null)    return false;
        char[] arr = word.toCharArray();
        if(pos == arr.length){
            if(node.word == null)   return false;
            //System.out.println(word);
            //System.out.println(node.word);
            //return node.word.matches(word);
            return true;
        }
        if(arr[pos] != '.'){
            return helper(word, node.next[arr[pos]-'a'], pos + 1);
        }else{
            for(int i = 0; i < 26; i++){
                if(helper(word, node.next[i], pos+1)){
                    return true;
                }
            }
        }
        return false;
    } // helper
}

// Your WordDictionary object will be instantiated and called as such:
// WordDictionary wordDictionary = new WordDictionary();
// wordDictionary.addWord("word");
// wordDictionary.search("pattern");
```

用递归的写法，最后不需要match~~(加了结果搞到0.99%)~~一定结果是正确的，遇到"."直接匹配下一个字符即可。