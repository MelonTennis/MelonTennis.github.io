---
layout: post
title: Leetcode - Trie Hard
subtitle: LC212, LC336, LC425, LC472
date: 2016-12-27
categories: Leetcode
tags: [Trie, String]
catalog: true
---

前缀树的hard题，一般前缀树与DFS BFS搜索或者其他数据结构结合起来~~搞一天之类~~。

### LC. 212 Word Search II

Given a 2D board and a list of words from the dictionary, find all words in the board. Each word must be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

Analysis:

> [Discuss, 优化的过程](https://discuss.leetcode.com/topic/33246/java-15ms-easiest-solution-100-00) 

前缀树 + dfs， 注意没找到一个word就要移除这个word，求掉duplicate。搜索每一个word时，每个字母不可以使用两次。所以先置为“*”，然后再恢复。当没有搜到word时剪枝，提高算法的效率。

```java
public class Solution {
    //tries
    class TrieNode{
        TrieNode[] next = new TrieNode[26];
        String word;
    }
    
    public List<String> findWords(char[][] board, String[] words) {
        List<String> res = new ArrayList<String>();
        TrieNode root = bulidTrie(words);
        for(int i = 0; i < board.length; i++){
            for(int j = 0; j < board[0].length; j++){
                dfs(board, i, j, root, res);
            }
        }
        return res;
    }

    public void dfs(char[][] board, int x, int y, TrieNode p, List<String> res){
        char cur = board[x][y];
        if(p == null)   return;
        if(cur == '*' || p.next[cur - 'a'] == null) return; // pruning
        p = p.next[cur - 'a'];
        if(p.word != null){
            res.add(p.word);
            p.word = null; // remove duplicate
        }
        
        board[x][y] = '*';
        if(x > 0)   dfs(board, x-1, y, p, res);
        if(y > 0)   dfs(board, x, y-1, p ,res);
        if(x < board.length-1) dfs(board, x+1, y, p, res);
        if(y < board[0].length-1) dfs(board, x, y+1, p, res);
        board[x][y] = cur; 
    }
    
    public TrieNode bulidTrie(String[] words){
        if(words == null || words.length == 0)  return null;
        TrieNode root = new TrieNode();
        for(String word: words){
            TrieNode p = root;
            for(char c: word.toCharArray()){
                int i = c - 'a';
                if(p.next[i] == null)   p.next[i] = new TrieNode();
                p = p.next[i];
            }
            p.word = word;
        }
        return root;
    }
}
```

### LC336. Palindrome Pairs

Given a list of **unique** words, find all pairs of **distinct** indices `(i, j)` in the given list, so that the concatenation of the two words, i.e. `words[i] + words[j]` is a palindrome.

**Example 1:**
Given `words` = `["bat", "tab", "cat"]`
Return `[[0, 1], [1, 0]]`
The palindromes are `["battab", "tabbat"]`

这题做了好久。。。首先构造所有words的前缀树结构，然后对每个word的reverse搜索，分别分情况进行讨论。当存在空String的情况时要注意去掉duplicate情况~~(用Set作弊不可取啊不可取)~~ 

```java
public class Solution {
    // tries 
    class Node{
        String word;
        Node[] next;
        int index;
        public Node(){
            word = null;
            next = new Node[26];
        }
    } // Node
    
    public List<List<Integer>> palindromePairs(String[] words) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        // build tree
        Node root = new Node();
        int i = 0;
        for(String word: words){
            Node node = root;
            for(char c: word.toCharArray()){
                if(node.next[c - 'a'] == null)  node.next[c - 'a'] = new Node();
                node = node.next[c - 'a'];
            } // for c
            node.word = word;
            node.index = i;
            i++;
        } // for word
        // search
        for(int ii = 0; ii< words.length; ii++){
            search(words[ii], root, res, ii);
        }
        return res;
    } // 
    
    public void search(String word, Node root, List<List<Integer>> res, int index){
        char[] arr = word.toCharArray();
        int len = arr.length;
        Node node = root;
        boolean flag = true;
        if(root.word != null && root.word.equals("")){
            if(isPan(word) && root.index != index){
                res.add(Arrays.asList(root.index, index));
            }
        }
        for(int i = len-1; i >= 0; i--){
            if(node.next[arr[i] - 'a'] != null){
                node = node.next[arr[i] - 'a'];
                if(node.word != null){
                    int len2 = node.word.length();
                    if(len2 == len && node.index != index){
                        res.add(Arrays.asList(node.index, index));
                    } // same length
                    else if(len2 < len){
                            if(isPan(word.substring(0, len-len2))){
                                res.add(Arrays.asList(node.index, index));
                            }
                        }
                    } // if word
                }else{
                    flag = false;
                    break;
                }
            } // for i
            if(flag) searchNode(node, res, index, len);
    } // search
    
    public boolean isPan(String word){
      //  System.out.println(word);
        char[] arr = word.toCharArray();
        for(int i = 0; i < arr.length/2; i++){
            if(arr[i] != arr[arr.length-1-i]){
                return false;
            }
        }
        return true;
    }
    
    public void searchNode(Node root, List<List<Integer>> res, int index, int pos) {
        Node cur = root;
        if(cur == null)   return;
        // if cur.word.length == pos that means word == cur.word (""), or duplicate adding
        // because here we only search for another word that is longer than current word
        if(cur.word != null && isPan(cur.word.substring(pos)) && index != cur.index && cur.word.length() != pos){
            res.add(Arrays.asList(cur.index, index));
        }
        for(int i = 0; i < 26; i++ ){
            if(cur.next[i] != null){
                searchNode(cur.next[i], res, index, pos);
            }
        }
    } // searchNode
}
```

~~这题告诉我，想要省事就要多些几个函数，优化函数的结构，减少冗余代码，才能省事。。。🙄多么痛的领悟~~

Time and Space: O(n*k^2), 其中n为words.length, k为其平均长度, O(n)

### LC425. Word Squares 

Given a set of words **(without duplicates)**, find all [word squares](https://en.wikipedia.org/wiki/Word_square)👇 you can build from them. A sequence of words forms a valid word square if the *k*th row and column read the exact same string, where 0 ≤ *k* < max(numRows, numColumns). 

```
b a l l
a r e a
l e a d
l a d y
```

> [Trie solution](https://discuss.leetcode.com/topic/64770/java-dfs-trie-54-ms-98-so-far/2)

首先构造words的Trie，对于构造一个和行数相同的Node array。递归寻找解，如果能构成word square则加入res中。对于每一行，从左至右查找，word square满足对角相似。

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
            } // col == row, another row
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

###  LC472. Concatenated Words

Given a list of words (**without duplicates**), please write a program that returns all concatenated words in the given list of words. A concatenated word is defined as a string that is comprised entirely of at least two shorter words in the given array.

**Example:**

```
Input: ["cat","cats","catsdogcats","dog","dogcatsdog","hippopotamuses","rat","ratcatdogcat"]

Output: ["catsdogcats","dogcatsdog","ratcatdogcat"]

Explanation: "catsdogcats" can be concatenated by "cats", "dog" and "cats"; 
 "dogcatsdog" can be concatenated by "dog", "cats" and "dog"; 
"ratcatdogcat" can be concatenated by "rat", "cat", "dog" and "cat".
```

此题用DP也可解，但是我非用Trie，首先注意entirely，~~坑~~.还要考虑不同形式的构造。

1. DP solution. 对于每个word，如果找到了前缀存在就继续查找直到词尾。 O(n^2*k^2), 没想到最后一个test caseTLE.

   ```java
   public class Solution {
       // dp solution
       public List<String> findAllConcatenatedWordsInADict(String[] words) {
           List<String> dict = Arrays.asList(words);
           List<String> res = new ArrayList<String>();
           for(String word: words){
               if(helper(word, dict, 0)){
                   res.add(word);
               } // if
           } // for word
           return res;
       } 
       
       public boolean helper(String word, List<String> dict, int num){
           if(dict.contains(word) && num > 0)  return true;
           int len = word.length();
           for(int i = 1; i < len; i++){
               if(dict.contains(word.substring(0, i))){
                   if(helper(word.substring(i), dict, num+1)) return true;
               }
           }
           return false;
       } // helper
   }
   ```

   2. Trie ~~难以置信的写了3天交了十几个答案🙄~~

   首先构造Trie，经典的写法。再对每个word由root开始进行搜索。搜索的过程中记录已经找到的word数目。如果找到了某个单词，就从下一个char开始查找，substring包含>=2个单词就加入结果中。

   ```java
   public class Solution {
       // tries
       class Node{
           String word;
           Node[] next;
           public Node(){
               word = null;
               next = new Node[26];
           }
       }
       
       Node root = new Node();
       public List<String> findAllConcatenatedWordsInADict(String[] words) {
          // Arrays.sort(words);
         //  System.out.println(Arrays.asList(words));
           List<String> res = new ArrayList<String>();
           Node node = root;
           for(String word: words){
               node = root;
               for(char c: word.toCharArray()){
                   if(node.next[c - 'a'] == null){
                       node.next[c - 'a'] = new Node();
                   } // if
                   node = node.next[c - 'a'];                
               } // for c
               node.word = word;
           } // construct tries
           for(String word: words){
               if(helper(word, root,0)){
                   res.add(word);
               }
           }
           //List<String> r = new ArrayList(res);
           //Collections.sort(r);
           return res;
       } // findAll
       
       public boolean helper(String word, Node root, int count){
           if (search(word, root) && count > 0)
   			return true;
   		for (int i = 1; i < word.length(); i++) {
   			if (search(word.substring(0, i), root))
   				if (helper(word.substring(i), root, count+1))
   					return true;
   		}
   		return false;
       } // helper
       
        public boolean search(String word, Node root) {
           Node cur = root;
           if(cur==null || word == null){
               return false;
           }
           int pos = 0;
           char[] arr = word.toCharArray();
           for(int i = 0; i < arr.length; i++ ){
               if(cur.next[arr[i] - 'a'] != null){
                   cur = cur.next[arr[i] - 'a'];
                   pos = i;
               }else{
                   return false;
               }
           }
           if(pos == arr.length-1) return cur.word != null;
           else return false;
       } // search
   }
   ```

   O(n*k^2)

   ~~做hard题有一种力不从心的感觉／告诉我要适时放弃🙄~~