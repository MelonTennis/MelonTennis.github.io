---
layout: post
title: Leetcode - Merge Intervals
subtitle: 
date: 2019-08-25
categories: Leetcode
tags: [Greedy]
description: 
catalog: true
---

## Merge Intervals

一堆merge intervals的问题，基本上都是通过排序 + greedy来解决的。

### 56. Merge Intervals - M

Merge intervals. 很多种解法，可以把start和end分别排序，找到end对应的starts，新建新的interval加入结果序列中。O(N * lgN), O(N).

```java
public class Solution {
    public List<Interval> merge(List<Interval> intervals) {
        List<Interval> res = new ArrayList<Interval>();
        if(intervals == null || intervals.size() == 0)  return res;
        // construct queue
        int n = intervals.size();
        int start[] = new int[n];
        int end[] = new int[n];
        for(int i = 0; i < n; i++){
            start[i] = intervals.get(i).start;
            end[i] = intervals.get(i).end;
        } // in
        Arrays.sort(start);
        Arrays.sort(end);
        for(int i= 0, j = 0; i < n; i++){
            if(i == n-1 || start[i+1] > end[i]){
                res.add(new Interval(start[j], end[i]));
                j = i+1;
            }
        }
        return res;
    }
}
```



###252. Meeting Rooms - S

简单的排序问题。O(N * lgN), O(1).

### 253. Meeting Rooms II - M

求最少需要的room数量，也就是没有overlap的interval可以共用一个room。可以用Heap也可以Sort来解决。复杂度都是O(N * lgN), O(1).

Heap的解法把当前所有的end放在最小堆中，每次新的meeting都会选择那个最先结束的room来安排。如果结束的比开始晚就证明需要一个新的room。Greedy的想法。

```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        Arrays.sort(intervals, new Comparator<int[]>() {
           @Override
           public int compare(int[] n1, int[] n2) {
               if(n1[0] != n2[0])   return n1[0] - n2[0];
               else return n1[1] - n2[1];
           }
        });
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        for(int[] interval: intervals) {
            if(pq.size() > 0 && interval[0] >= pq.peek()) {
                pq.poll();
            }
            pq.offer(interval[1]);
        }
        return pq.size();
    }
}
```

排序的方法分别对start和end进行排序，和merge interval相似，对于每一个起始时间，如果它小于前一个结束时间说明产生overlap，需要一个新的room。

```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        if(intervals == null || intervals.length == 0)  return 0;
        int[] starts = new int[intervals.length];
        int[] ends = new int[intervals.length];
        for(int i = 0; i < intervals.length; i++) {
            starts[i] = intervals[i][0];
            ends[i] = intervals[i][1];
        }
        Arrays.sort(starts);
        Arrays.sort(ends);
        int e = 0, res = 1;
        for(int i = 1; i < starts.length; i++) {
            if(starts[i] < ends[e]) {
                res++;
            } else {
                e++;
            }
        }
        return res;
    }
}
```



### 452. Minimum Number of Arrows to Burst Balloons - M

求有多少个merged intervals。O(N * lgN), O(1). 这题的test case有Integer.MIN_VALUE, Integer.MAX_VALUE. 如果i从0开始不能够将end初始值设为0或者Integer.MIN_VALUE。

```java
public class Solution {
    public int findMinArrowShots(int[][] points) {
        // finding the none overlap intervals
        if(points == null || points.length == 0)    return 0;
        Arrays.sort(points, new Comparator<int[]>(){
            public int compare(int[] a, int[] b){
                return a[1] - b[1];
            }
        });
        int end = 0, count = 1;
        for(int i = 1; i < points.length; i++){
            if(points[i][0] > end){
                end = points[i][1];
                count++;
            }
        } // for i
        return count;
    }
}
```



### 435. Non-overlapping Intervals - M

求需要保证不overlap而需要去掉的最少interval数量。用Greedy + Sort可以解决。排序所有intervals后，出现overlap就留下end小的。O(N * lgN), O(1).

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        if(intervals == null || intervals.length <= 1) {
             return 0;
        }
        Arrays.sort(intervals, new Comparator<int[]>(){
            @Override
            public int compare(int[] l1, int [] l2) {
                return l1[0] == l2[0]? l1[1] - l2[1]: l1[0] - l2[0];
            }
        });
        int i = 0, j = 1, res = 0;
        while(j < intervals.length) {
            int[] pre = intervals[i];
            int[] cur = intervals[j];
            if(Math.max(pre[0], cur[0]) < Math.min(pre[1], cur[1])) {
                res++;
                if(pre[1] > cur[1]) {
                    i = j;
                    j = j + 1;
                } else j++;
            } else {
                i = j;
                j = i + 1;
            }
        }
        return res;
    }
}
```

