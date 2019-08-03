---
layout: post
title: Leetcode - Course Schedule
subtitle: Course Schedule - topologicalsort
date: 2016-12-22
categories: Leetcode
tags: [TopologicalSort, BFS]
catalog: true
---

### LC207. Course Schedule 

[Topological Sort](http://www.personal.kent.edu/~rmuhamma/Algorithms/MyAlgorithms/GraphAlgor/topoSort.htm)

[Topological sort via dfs](https://www.geeksforgeeks.org/topological-sorting/)

There are a total of *n* courses you have to take, labeled from `0` to `n - 1`. Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: `[0,1]` Given the total number of courses and a list of prerequisite **pairs**, is it possible for you to finish all courses?

Topological Sort + BFS, O(V^2)

~~邻接表能做吗?~~

```java
public class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        // topological sort
        int[] indegree = new int[numCourses];
        for(int[] pre: prerequisites){
            indegree[pre[1]]++;
        } // for
        Queue<Integer> queue = new LinkedList<Integer>();
        for(int i = 0; i < numCourses; i++){
            if(indegree[i] == 0){
                queue.add(i);
            }
        } // for i
        while(!queue.isEmpty()){
            numCourses--;
            int c = queue.poll();
            for(int[] pair: prerequisites){
                if(pair[0] == c){
                    indegree[pair[1]]--;
                    if(indegree[pair[1]] == 0){
                     queue.add(pair[1]);
                     }
                }
            }
        }
        return numCourses == 0;
    }
}
```

### LC210 Course Schedule II

Return the order of course that should take. If impossible return empty array.

和上面一样。

⚠️： prerequisties[0, 1] , 1->0 indegree[0]++

```java
public class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] res = new int[numCourses];
        Queue<Integer> queue = new LinkedList<Integer>();
        int[] indegree = new int[numCourses];
        for(int i = 0; i < prerequisites.length; i++) {
            indegree[prerequisites[i][0]]++;
        } // for i
        for(int i = 0; i < numCourses; i++){
            if(indegree[i] == 0){
                queue.offer(i);
            }
        } // for i
        
        int count = 0 , k = 0;
        while(!queue.isEmpty()){
            int course = queue.poll();
            res[k] = course;
            k++;
            count++;
            for(int i = 0; i < prerequisites.length; i++){
                if(prerequisites[i][1] == course){
                    indegree[prerequisites[i][0]]--;
                    if(indegree[prerequisites[i][0]] == 0){
                        queue.offer(prerequisites[i][0]);
                    }
                }
            }
        }
        return count == numCourses?res:new int[0];
    }
}
```

