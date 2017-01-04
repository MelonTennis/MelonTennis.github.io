---
layout: post
title: Leetcode - Union Find
subtitle: LC305, LC261, LC323
date: 2017-01-03
categories: Leetcode
tags: [UnionFind]
catalog: true
---

###  Leetcode - Union Find

Union Find 是我自学的第一个算法，总觉得很有感情啊。我还记得是跟着普林斯顿的公开课学的呢:D 总是忘记...每次遇到久恰似故人来:D

 [PPT](https://www.cs.princeton.edu/~rs/AlgsDS07/01UnionFind.pdf) 这PPT画风好像不太一样了

[Algorithm and DataStructure Note](https://www.cs.princeton.edu/~rs/AlgsDS07/) 我最初的算法入门课Note, 说起来这课课件里的图做的实在是好...

#### LC305. Number of Islands II

A 2d grid map of `m` rows and `n` columns is initially filled with water. We may perform an *addLand* operation which turns the water at position (row, col) into a land. Given a list of positions to operate, **count the number of islands after each addLand operation**. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

**Example:**

Given `m = 3, n = 3`, `positions = [[0,0], [0,1], [1,2], [2,1]]`.
Initially, the 2d grid `grid` is filled with water. (Assume 0 represents water and 1 represents land).

```
0 0 0
0 0 0
0 0 0

```

Operation #1: addLand(0, 0) turns the water at grid[0][0] into a land.

```
1 0 0
0 0 0   Number of islands = 1
0 0 0

```

Operation #2: addLand(0, 1) turns the water at grid[0][1] into a land.

```
1 1 0
0 0 0   Number of islands = 1
0 0 0

```

Operation #3: addLand(1, 2) turns the water at grid[1][2] into a land.

```
1 1 0
0 0 1   Number of islands = 2
0 0 0

```

Operation #4: addLand(2, 1) turns the water at grid[2][1] into a land.

```
1 1 0
0 0 1   Number of islands = 3
0 1 0

```

We return the result as an array: `[1, 1, 2, 3]`

**Challenge:**

Can you do it in time complexity O(k log mn), where k is the length of the `positions`?

阅读理解题:D 这题的medium版本是非动态的，dfs可解，每次遇到1dfs，并将dfs到的都改成0就可以了，那么动态版本的如果这样做，时间一定很糟糕:D 怎么做呢？

[低卡百事的解法](https://discuss.leetcode.com/topic/29518/java-python-clear-solution-with-unionfind-class-weighting-and-path-compression)

利用Union Find来解，每次加入一个新位置时，假设是一个新的岛屿，再查找相邻的是否有联通的节点，如果找到了就减去加上的count。同时进行路径压缩。Union Find的下标用m*n的array表示即可。重点是在于如果加入了一个点，然后把好几个岛连起来要怎么处理。就是在于当前节点隔壁的节点union的root值，如果不是当前的root，证明存在将几个岛连在一起的情况。每次union的时候要重新将cur的root置为union后的root。

```java
// UnionFind
public class Solution {
    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        List<Integer> res = new ArrayList<Integer>();
        if(positions == null || m == 0 || n == 0){
            return res;
        } // bad input
        int count = 0;
        int[] union = new int[m * n];
        Arrays.fill(union, -1);
        int[] dx = {0 , 0, 1, -1}, dy = {1, -1, 0, 0};
        for(int[] pos : positions){
            int cur = pos[0]*n + pos[1];
            union[cur] = cur;
            count++;
            for(int i = 0; i < 4; i++){
                int x = pos[0] + dx[i], y = pos[1] + dy[i];
                if(x >=0 && x < m && y >= 0 && y < n && union[x*n + y] != -1){
                    int r = findroot(union, x*n + y);
                    if(union[cur] != r){ // not union yet, union
                        union[cur] = r; // union
                        cur = r; // set root
                        count--; // deduct duplicate
                    }
                }
            }
            res.add(count);
        }
        return res;
    } // numIsland
    
    public int findroot(int[] union, int num){
        while(union[num] != num){
            union[num] = union[union[num]]; // path compress
            num = union[num];
        } 
        return num;
    } // findroot
}
```

根据discuss，时间复杂度👇

> `O((N + Mlog* N)` where `M` is the number of operations ( unite and find ), `N` is the number of objects, `log*` is [iterated logarithm](https://en.wikipedia.org/wiki/Iterated_logarithm) while the naive runs in `O(MN)`.

#### LC261. Graph Valid Tree 

Given `n` nodes labeled from `0` to `n - 1` and a list of undirected edges (each edge is a pair of nodes), write a function to check whether these edges make up a valid tree.

For example:

Given `n = 5` and `edges = [[0, 1], [0, 2], [0, 3], [1, 4]]`, return `true`.

Given `n = 5` and `edges = [[0, 1], [1, 2], [2, 3], [1, 3], [1, 4]]`, return `false`.

> According to the [definition of tree on Wikipedia](https://en.wikipedia.org/wiki/Tree_(graph_theory)): “a tree is an undirected graph in which any two vertices are connected by *exactly* one path. In other words, any connected graph without simple cycles is a tree.”

无向图 + 联通 -> union find

[Union Find](https://discuss.leetcode.com/topic/21712/ac-java-union-find-solution/29)

```java
public class Solution {
    public boolean validTree(int n, int[][] edges) {
        // union find
        // no isolate island, can muliple root
        if(n <= 0)  return false;
        int[] union = new int[n];
        // initialize 
        for(int i = 0; i < n; i++)  union[i] = i;
        for(int[] edge: edges){
            int f1 = find(union, edge[0]);
            int f2 = find(union, edge[1]);
            if(f1 == f2)    return false; // cycle
            union[f2] = f1; // connect
        }
        // need n-1 edges to connect n points
        return edges.length == n-1;
    }
    
    // find num's root
    public int find(int[] union, int num){
        while(union[num] != num){
            // compress path
            union[num] = union[union[num]];
            num = union[num];
        }
        return num;
    }
}
```

一开始尝试用邻接表但是失败了。看到了discuss里有这个解答。

[Adjacncy list](https://discuss.leetcode.com/topic/21714/ac-java-graph-dfs-solution-with-adjacency-list)

```java
public class Solution {
    public boolean validTree(int n, int[][] edges) {
        // initialize adjacency list
        List<List<Integer>> adjList = new ArrayList<List<Integer>>(n);
        
        // initialize vertices
        for (int i = 0; i < n; i++)
            adjList.add(i, new ArrayList<Integer>());
        
        // add edges    
        for (int i = 0; i < edges.length; i++) {
            int u = edges[i][0], v = edges[i][1];
            adjList.get(u).add(v);
            adjList.get(v).add(u);
        }
        
        boolean[] visited = new boolean[n];
        
        // make sure there's no cycle
        if (hasCycle(adjList, 0, visited, -1))
            return false;
        
        // make sure all vertices are connected
        for (int i = 0; i < n; i++) {
            if (!visited[i]) 
                return false;
        }
        
        return true;
    }
    
    // check if an undirected graph has cycle started from vertex u
    boolean hasCycle(List<List<Integer>> adjList, int u, boolean[] visited, int parent) {
        visited[u] = true;
        
        for (int i = 0; i < adjList.get(u).size(); i++) {
            int v = adjList.get(u).get(i);
            
            if ((visited[v] && parent != v) || (!visited[v] && hasCycle(adjList, v, visited, u)))
                return true;
        }
        
        return false;
    }
}
```

诶我知道我哪里写错了... 果然是环写错了QAQ

#### LC323. Number of Connected Components in an Undirected Graph

Given `n` nodes labeled from `0` to `n - 1` and a list of undirected edges (each edge is a pair of nodes), write a function to find the number of connected components in an undirected graph.

**Example 1:**

```
     0          3
     |          |
     1 --- 2    4

```

Given `n = 5` and `edges = [[0, 1], [1, 2], [3, 4]]`, return `2`.

**Example 2:**

```
     0           4
     |           |
     1 --- 2 --- 3

```

Given `n = 5` and `edges = [[0, 1], [1, 2], [2, 3], [3, 4]]`, return `1`.

这个题也是很典型的Union Find，拿到之后不做他想。不过这些Union Find的图像搜索类问题想来都可以用DFS或者BFS来解，就向D&C经常可以用DP？

```java
public class Solution {
    public int countComponents(int n, int[][] edges) {
        // union find
        int[] union = new int[n];
        Set<Integer> set = new HashSet<Integer>();
        for(int i = 0; i < n; i++)  union[i] = i;
        for(int[] edge: edges){
            int root1 = find(union, edge[1]);
            int root2 = find(union, edge[0]);
            if(root1 != root2){
                n--;
                union[root1] = union[root2];
            }
        }
        return n;
    }
    
    private int find(int[] union, int r){
        while(r != union[r]){
            union[r] = union[union[r]];
            r = union[r];
        }
        return r;
    }
}
```

> `O((N + Mlog* N)` where `M` is the number of operations ( unite and find ), `N` is the number of objects, `log*` is [iterated logarithm](https://en.wikipedia.org/wiki/Iterated_logarithm) while the naive runs in `O(MN)`.

Quick Find的时间复杂度如同PPT里讲的那样，带有路径压缩的话👆