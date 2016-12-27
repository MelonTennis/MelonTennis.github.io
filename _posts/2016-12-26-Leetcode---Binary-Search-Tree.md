---
layout: post
title: Leetcode - Binary Search Tree
subtitle: LC220, LC315, LC327, LC352
date: 2016-12-26
categories: Leetcode
tags: [BST, TreeSet, TreeMap]
catalog: true
---

### LC220. Duplicate III

Given an array of integers, find out whether there are two distinct indices *i* and *j* in the array such that the difference between **nums[i]** and **nums[j]** is at most *t* and the difference between *i* and *j* is at most *k*.

3个月前做的题果然已经想不起来了。~~所以还是持续过🎄比较好（笑）~~用到了TreeSet~~什么鬼这不是我三天前才新知道的吗~~，具体用法和HashSet相似，不过是一个有序的Set。然后还有一个叫做TreeMap的东西。~~😱~~

> [TreeSet & TreeMap](http://shmilyaw-hotmail-com.iteye.com/blog/1836431)
>
> [Java集合Tree](http://blog.csdn.net/speedme/article/details/22661671)
>
> [HashSet VS. TreeSet](http://javahungry.blogspot.com/2014/03/difference-between-hashset-and-treeset-similarities-and-example.html)
>
> [Class TreeSet](https://docs.oracle.com/javase/7/docs/api/java/util/TreeSet.html)

一直维护一个具有***k***个元素的Set👇

> [Discuss](https://discuss.leetcode.com/topic/15191/java-o-n-lg-k-solution)

```java
public class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        if(nums == null || k <= 0 || nums.length <= 0) {
            return false;
        }
        TreeSet<Integer> tree = new TreeSet<Integer>();
        for(int i = 0; i < nums.length; i++){
            Integer floor = tree.floor(nums[i]+t);
            Integer ceiling = tree.ceiling(nums[i]-t);
            if((floor != null && floor >= nums[i]) || (ceiling != null && ceiling <= nums[i])){
                return true;
            }
            tree.add(nums[i]);
            if (i >= k) {
                tree.remove(nums[i - k]);
            }
        }
        return false;
    }
}
```

Time & Space: O(nlgn), O(n)

### LC315. Count of Smaller Numbers After Self

You are given an integer array *nums* and you have to return a new *counts* array. The *counts* array has the property where `counts[i]` is the number of smaller elements to the right of `nums[i]`.

诶这题，[BIT／Segment Tree](https://yijiajin.github.io/leetcode/2016/12/22/Leetcode-Segment-Tree-Binary-Index-Tree/) 解法

在构造Binary Search Tree的时候同时维护当前节点的duplicate数量（dup）和当前节点左下方的所以节点数量（sum）。这样每当插入一个比较大的树，我们需要的count值就是在插入这个节点时所有向右的节点的dup+sum和。~~他们怎么那么聪明😳~~ 

> [Discuss BST solution])(https://discuss.leetcode.com/topic/31405/9ms-short-java-bst-solution-get-answer-when-building-bst)

```java
public class Solution {
    class Node {
        Node left;
        Node right;
        int val;
        int sum;
        int dup = 1;
        public Node(int v, int s){
            val = v;
            sum = s;
        }
    } // Node
    
    public List<Integer> countSmaller(int[] nums) {
        // record result
        Integer[] ans = new Integer[nums.length];
        Node root = null;
        // build from end to begin
        for(int i = nums.length-1; i >=0; i--){
            root = insert(nums[i], root, ans, i, 0);
        } // for
        return Arrays.asList(ans); // int[] cannot use this method
    } // countSmaller
    
    // building BST and maintain sum and duplicate
    private Node insert(int num, Node node, Integer[] ans, int i, int preSum){
        if(node == null){   // the last item || new children
            node = new Node(num, 0);
            ans[i] = preSum;
        }else if(node.val == num){  // duplicate items
            node.dup++;
            ans[i] = preSum + node.sum;
        }else if(node.val > num){ // new left child
            node.sum++;
            node.left = insert(num, node.left, ans, i, preSum);
        }else{
            node.right = insert(num, node.right, ans, i, preSum + node.dup + node.sum);
        }
        return node;
    }
}
```

O(nlgn)

### LC327. Count of Range Sum

Given an integer array `nums`, return the number of range sums that lie in `[lower, upper]` inclusive. Range sum `S(i, j)` is defined as the sum of the elements in `nums` between indices `i` and `j` (`i` ≤ `j`), inclusive.

这题要计算满足和在（lower，upper）之间的区间有多少。

Analysis: ~~Hard题做一天也是很正常的吧😂~~

1. Segment Tree解法，总觉得区间和问题应该用segment tree能做，但是一直不能AC最后还是~~扒~~参考了[discuss](https://discuss.leetcode.com/topic/33734/java-segmenttree-solution-36ms/4)才AC。

   题目求***lower <= sum(i,  j)  <= upper***, 即构造sum数列，其中sum[i] = sum(nums[0]~nums[i])，所求转化为***lower + sum[i] <= sum[j] <= upper+sum[i]***. 线段树将一个数列按照二叉树的结构分成一些单元区间，对于每个区间存在上线边界，利用此边界比较大小可以得到在此区间的元素个数。利用这个特性，首先求出对于***nums***所有元素存在的不同区间和构造成线段树，其中每个Node记录了排序好的每个区间和的左右index，sum初始化为0。然后从index0扫描***nums***，对于每个sum[i]，要统计的是线段树中满足lower <= sum[i] - sum[k]  <= upper的元素k数量，即**sum[k] <= -lower + sum[j] && sum[i] >= sum[j] -upper** (i >= k)。对于***i***，将index <= i的区间和sum加入线段树中进行更新，统计个数。最后得到结果。 ~~好难理解🙄关爱傻子的眼神看着自己~~

```java
public class Solution {
    Long[] arr;
    // segment tree
    class Node{
        int start;
        int end;
        int sum;
        Node left;
        Node right;
        public Node(int s, int e){
            start = s;
            end = e;
            sum = 0;
            left = null;
            right = null;
        }
    } // Node
    
    public Node buildTree(int start, int end){
        if(start > end) return null;
        Node root = new Node(start, end);
        if(start == end)    return root;
        else{
            int mid = start + (end - start)/2;
            root.left = buildTree(start, mid);
            root.right = buildTree(mid+1, end);
        }
        return root;
    } // build segment tree
    
    
    public int count(Node root, long lower, long upper){
        if(root == null)    return 0;
        if (arr[root.start] >= lower && arr[root.end] <= upper) return root.sum;
        if (arr[root.start] > upper || arr[root.end] < lower)  return 0;
        if (root.start == root.end) return 0;
        long mid = arr[(root.start+root.end)/2];
        if (mid >= upper) return count(root.left, lower, upper);
        if (mid < lower) return count(root.right, lower, upper);
        return count(root.left, lower, mid) + count(root.right, arr[(root.start+root.end)/2+1], upper);
    } // count
    
    public void add(Node root, long num){
        if(root == null)  return;
        if (root.start == root.end) {root.sum++; return;}
        root.sum++;
        long mid = arr[(root.start+root.end)/2];
        if(num <= mid){
            add(root.left, num);
        }else{
            add(root.right, num);
        }
    }
    
    public int countRangeSum(int[] nums, int lower, int upper) {
        int res = 0;
        if(nums == null || nums.length == 0 || lower > upper)   return 0;
        int len = nums.length;
        long sum = 0;
        Set<Long> set = new HashSet<>();
        set.add(sum);
        for (int i = 0; i < nums.length; i++) {
            sum = sum + nums[i]; 
            set.add(sum);
        }
        arr = set.toArray(new Long[0]); 
        Arrays.sort(arr);
        Node root = buildTree(0, arr.length-1);
        sum = 0;
        add(root, 0);
        for(int i = 0; i < len; i++){
            sum += nums[i];
            int temp = 0;
            temp = count(root, sum - upper, sum - lower);
            res += temp;
            add(root, sum);
        }
        return res;
    }
}
```

O(nlgn), 时间不是最佳(40ms)但是这种思路还是很棒的。

2. MergeSort solution 

   这是~~up主~~题主[低卡百事大神的解法](https://discuss.leetcode.com/topic/33738/share-my-solution),  我觉得我应该祈祷不要遇到这种题。。。

   同样的对于***i***, 求***j - k***, 其中

   - `j` is the first index satisfy `sums[j] - sums[i] > upper` and
   - `k` is the first index satisfy `sums[k] - sums[i] >= lower`.

   利用归并排序的思路进行计数，用long以防止int的溢出。

```java
public class Solution {
    public int countRangeSum(int nums[], int lower, int upper){
        int n = nums.length;
        long[] sums = new long[n+1];
        for(int i = 0; i < n; i++){
            sums[i+1] = sums[i] + nums[i];
        }
        return merge(sums, 0, n+1, lower, upper);
    }
    
    public int merge(long sums[], int start, int end, int lower, int upper){
        if(end <= start+1) return 0;
        int mid = (end + start)/2;
        int count = merge(sums, start, mid, lower, upper) + merge(sums, mid, end, lower, upper);
        int j = mid, k = mid, t = mid;
        long[] cache = new long[end-start];
        for(int i = start, r = 0; i < mid; i++, r++){
            while(k < end && sums[k] - sums[i] < lower) k++;
            while(j < end && sums[j] - sums[i] <= upper) j++;
            while(t < end && sums[t] < sums[i]) cache[r++] = sums[t++];
            cache[r] = sums[i];
            count += j-k;
        }
        System.arraycopy(cache, 0, sums, start, t-start);
        return count;
    }
}
```

O(nlgn), 10s. ~~虽然都是nlgn，但是~~ 膜拜各路大神🌝。

### LC352. Data Stream as Disjoint Intervals

Given a data stream input of non-negative integers a1, a2, ..., an, ..., summarize the numbers seen so far as a list of disjoint intervals.

For example, suppose the integers from the data stream are 1, 3, 7, 2, 6, ..., then the summary will be:

```
[1, 1]
[1, 1], [3, 3]
[1, 1], [3, 3], [7, 7]
[1, 3], [7, 7]
[1, 3], [6, 7]

```

**Follow up:**
What if there are lots of merges and the number of disjoint intervals are small compared to the data stream's size?

Analysis:

我在认真的想能不能把merge interval的代码扒过来的事情。。。后来看了[discuss](https://leetcode.com/problems/data-stream-as-disjoint-intervals/)，看到TreeMap就像看到了新大陆~~果然忘记了三个月前做的TreeSet~~

TreeSet和TreeMap由于具有二叉树的结构，还记录上下边界对于intervals的题目很好用。对于存在的intervals和新加入的num情况分别讨论更新即可。

```java
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
public class SummaryRanges {

    // use TreeMap 
    TreeMap<Integer, Interval> tree;
    /** Initialize your data structure here. */
    public SummaryRanges() {
        tree = new TreeMap<Integer, Interval>(); 
    }
    
    public void addNum(int val) {
        if(tree.containsKey(val)) return;
        Integer left = tree.lowerKey(val); // key just below val
        Integer right = tree.higherKey(val); // key just above val
        // link two existed intervals
        if(left != null && right != null && tree.get(left).end == val-1 && tree.get(right).start == val + 1){
            tree.get(left).end = tree.get(right).end;
            tree.remove(right);
        }else if(left != null && tree.get(left).end+1 >= val){ // renew left intervals
            tree.get(left).end = Math.max(tree.get(left).end, val);
        }else if(right != null && tree.get(right).start == val + 1){ // renew right intervals
            tree.put(val, new Interval(val, tree.get(right).end));
            tree.remove(right);
        }else{ // new intervals
            tree.put(val, new Interval(val, val));
        }
    } // addNum
    
    public List<Interval> getIntervals() {
        return new ArrayList<>(tree.values());
    }
}

/**
 * Your SummaryRanges object will be instantiated and called as such:
 * SummaryRanges obj = new SummaryRanges();
 * obj.addNum(val);
 * List<Interval> param_2 = obj.getIntervals();
 */
```

Time & Space: add O(lgn), O(n)

> [Class TreeMap](http://docs.oracle.com/javase/7/docs/api/java/util/TreeMap.html)
>
> [Java Collections](http://infotechgems.blogspot.com/2011/11/java-collections-performance-time.html)

