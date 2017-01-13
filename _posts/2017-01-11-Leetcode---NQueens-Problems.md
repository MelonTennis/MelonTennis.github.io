---
layout: post
title: Leetcode - NQueens Problems
subtitle: LC51, LC52
date: 2017-01-11
categories: Leetcode
tags: [Backtracking]
catalog: true
---

#### LC51. N-Queens

The *n*-queens puzzle is the problem of placing *n* queens on an *n*×*n* chessboard such that no two queens attack each other. Given an integer *n*, return all distinct solutions to the *n*-queens puzzle.

Each solution contains a distinct board configuration of the *n*-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space respectively.

N皇后问题，这是我还没转CS就听说过的问题啊(笑)。虽然有更厉害的算法不过我还是用最好理解的回溯法吧。

> [Java code](https://discuss.leetcode.com/topic/8592/comparably-concise-java-code)

利用行数作为flag，每次对一行找到一个可行的放置皇后的方式就继续下一行，直到最后一行，就将当前的结果加入结果集中。利用三个boolean数组分别记录每一列，和左右对角线上是否存在皇后，存在即置为true。

```java
public class Solution {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> res = new ArrayList<List<String>>();
        helper(res, new ArrayList<String>(), new boolean[n], new boolean[2*n-1], new boolean[2*n-1], 0, n);
        return res;
    } // solve
    
    private void helper(List<List<String>> res, List<String> cur, boolean[] cols, boolean[] dia1, boolean[] dia2, int row, int n){
        if(row == n){
            res.add(new ArrayList<String>(cur));
            return;
        }else{
            for(int i = 0; i < n; i++){
                int d1 = row + n - 1 - i; // diagonal 1
                int d2 = row + i; // diagonal 2
                if(!cols[i] && !dia1[d1] && !dia2[d2]){
                    char[] rows = new char[n];
                    Arrays.fill(rows, '.');
                    rows[i] = 'Q';
                    cur.add(new String(rows));
                    cols[i] = dia1[d1] = dia2[d2] = true;
                    helper(res, cur, cols, dia1, dia2, row+1, n);
                    cols[i] = dia1[d1] = dia2[d2] = false;
                    cur.remove(cur.size() - 1);
                }
            } // for
        } // else
    } // helper
}
```

O(n^n), O(n)

#### LC52. N-Queens II

Follow up for N-Queens problem.

Now, instead outputting board configurations, return the total number of distinct solutions.

不需输出答案，只需输出答案的数量。一样的思路，增加一个全局的count，每次可以向下一行搜索就进行递归，搜索结束count++.

> [java solutoin](https://discuss.leetcode.com/topic/29626/easiest-java-solution-1ms-98-22)

```java
public class Solution {
    int count = 0;
    public int totalNQueens(int n) {
        helper(0, new boolean[n], new boolean[2*n - 1], new boolean[2*n - 1], n);
        return count;
    }
    
    private void helper(int row, boolean[] cols, boolean[] dia1, boolean[] dia2, int n){
        if(row == n){
            count++;
            return;
        }else{
            for(int c = 0; c < n; c++){
                int d1 = row + n - 1 - c;
                int d2 = row + c;
                if(!cols[c] && !dia1[d1] && !dia2[d2]){
                    cols[c] = dia1[d1] = dia2[d2] = true;
                    helper(row+1, cols, dia1, dia2, n);
                    cols[c] = dia1[d1] = dia2[d2] = false;
                } // if
            } // for c
        } // else
    } // helper
}
```

O(n^n), O(1)

