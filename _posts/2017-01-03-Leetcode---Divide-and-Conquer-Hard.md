---
layout: post
title: Leetcode - Divide and Conquer Hard
subtitle: LC4, LC23, LC218, LC282, LC312, LC315, LC327
date: 2017-01-03
categories: Leetcode
tags: [D&C]
catalog: true
---

###  Leetcode - Divide and Conquer Hard

#### LC4. Median of Two Sorted Arrays

here are two sorted arrays **nums1** and **nums2** of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

**Example 1:**

```
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```

> [Discuss](https://discuss.leetcode.com/topic/4996/share-my-o-log-min-m-n-solution-with-explanation)

思路如下：

对于数组A, B。中位数满足media 属于 C = merge(A, B), media >= left, media <= right. 所以要找到i, j， 满足A[i] >= B[j-1] && B[j] >= A[i-1]， i + j = (len1 + len2 + 1)/2。

```java
public class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
         // median is the num that separate the array to two equal length parts, left <= right
         // nums1[i-1] & nums2[j-1] -> whole num's left part; nums1[i] & nums2[j] -> right part
         // so we need to find i, enables nums1[i] >= nums2[j-1] && nums2[j] >= nums1[i-1]
         // binary search, so time cost: log(min(n1, n2))
         int n1 = nums1.length, n2 = nums2.length;
         if(n1 > n2)    return findMedianSortedArrays(nums2, nums1);
         // input is int, output is double, so should devide 2.0 not 2, and change type to double
         if(n1 == 0)    return n2%2==0?(nums2[n2/2]+nums2[n2/2-1])/2.0:(double)nums2[n2/2];
         int begin = 0, end = n1;
         while(begin <= end){
             int i = begin + (-begin + end)/2;
             int j = (n1 + n2 + 1)/2 - i;
             if(j >= 1 && i < n1 && nums1[i] < nums2[j - 1])    begin = i + 1;  
             else if(i >= 1 && j < n2 && nums2[j] < nums1[i - 1])    end = i - 1;
             else{
                 int maxleft = 0, minright = 0;
                 if(i == 0) maxleft = nums2[j-1];
                 else if(j == 0)    maxleft = nums1[i-1];
                 else   maxleft = Math.max(nums1[i-1], nums2[j-1]);
                 if(i == n1)    minright = nums2[j];
                 else if(j == n2)    minright = nums1[i];
                 else   minright = Math.min(nums2[j], nums1[i]);
                 
                 return (n1+n2)%2 == 0?(maxleft + minright)/2.0:(double)maxleft;
             }
         }
         return 0.0;
    }
}
```

⚠️：double形式的转换

####  LC23. Merge k Sorted Lists

Merge *k* sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

分治法的很明显:D   每两个list分别merge起来

O(nlgk) - Partilize - lgk, Merge - n

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        return partlize(lists, 0, lists.length-1);
    }
    
    private ListNode partlize(ListNode[] lists, int begin, int end){
        if(begin == end)    return lists[begin];
        if(begin > end) return null;
        int mid = begin + (end - begin)/2;
        ListNode l1 = partlize(lists, begin, mid);
        ListNode l2 = partlize(lists, mid+1, end);
        return merge(l1, l2);
    }
    
    private ListNode merge(ListNode l1, ListNode l2){
        if(l1 == null)  return l2;
        if(l2 == null)  return l1;
        if(l1.val < l2.val){
            l1.next = merge(l1.next, l2);
            return l1;
        }else{
            l2.next = merge(l2.next, l1);
            return l2;
        }
    }
}
```

#### LC218. The Skyline Problem

[LC218 ](https://yijiajin.github.io/leetcode/2016/12/22/Leetcode-Segment-Tree-Binary-Index-Tree/)

#### LC312. Burst Balloons 

[LC312](https://yijiajin.github.io/leetcode/2017/01/02/LC312.-Burst-Balloons/)

#### LC282. Expression Add Operators

Given a string that contains only digits `0-9` and a target value, return all possibilities to add **binary** operators (not unary) `+`, `-`, or `*`between the digits so they evaluate to the target value.

Examples: 

```
"123", 6 -> ["1+2+3", "1*2*3"] 
"232", 8 -> ["2*3+2", "2+3*2"]
"105", 5 -> ["1*0+5","10-5"]
"00", 0 -> ["0+0", "0-0", "0*0"]
"3456237490", 9191 -> []
```

加入运算符得到结果，我的想法就是对每个位置都加入三种运算符计算结果，如果得到要求结果就加入res中，否则不加入。感觉符合D&C，但是运算效率😶

> [Backtracing solution](https://discuss.leetcode.com/topic/24523/java-standard-backtrace-ac-solutoin-short-and-clear)

乘法的部分一开始没想明白，看了discuss知道要先剪去前面的计算值，再用上一个值✖️当前值再相加得到最后的结果。注意没有01之类的数字。这个要276ms。

```java
public class Solution {
    public List<String> addOperators(String num, int target) {
        List<String> res = new ArrayList<String>();
        if(num.length() == 0 || num == null){
            return res;
        }
        helper(res, target, num,  "", 0, 0, 0);
        return res;
    }
    
    public void helper(List<String> res, int target, String num, String path, int pos, long cur, long operator){
        if(pos == num.length()){
             if(cur == target) 
                res.add(path);
            return;
        }
        for(int i = pos; i < num.length(); i++){
            if(num.charAt(pos) == '0' && pos != i){
                break;
            }
            long current = Long.parseLong(num.substring(pos, i+1));
            if(pos == 0){
                helper(res, target, num, path+current, i+1, current, current);
            }
            else{
                helper(res, target, num, path+"+"+current, i+1, current + cur, current);
                helper(res, target, num, path+"-"+current, i+1, cur-current, -current);
                helper(res, target, num, path+"*"+current, i+1, cur-operator+operator*current, operator*current);
            }
        }
    }
}
```

> [beat 100%](https://discuss.leetcode.com/topic/35942/java-ac-solution-19ms-beat-100-00)

```java
public class Solution {
    public List<String> addOperators(String num, int target) {
        char[] digit = num.toCharArray(), path = new char[num.length() * 2]; 
        List<String> ans = new ArrayList();
        long pureNum = 0;
        for (int i = 0; i < digit.length; i++) {
            pureNum = pureNum * 10 + (long)(digit[i] - '0');
            path[i] = digit[i];
            dfs(i + 1, i + 1, 0, pureNum, path, digit, target, ans);
            if (pureNum == 0) break; // not allow number with 0 prefix, except zero itself;
        }
        return ans;
    }
    
    private void dfs(int ip, int id, long toAdd, long toMult, char[] path, char[] digit, int target, List<String> ans) {
        if (id == digit.length && toAdd + toMult == target) {
            ans.add(String.valueOf(path, 0, ip));
            return;
        }
        long pureNum = 0;
        for (int i = 0; id + i < digit.length; i++) {
            pureNum = pureNum * 10 + (long)(digit[id + i] - '0');
            path[ip + i + 1] = digit[id + i];
            path[ip] = '+';
            dfs(ip + i + 2, id + i + 1, toAdd + toMult, pureNum, path, digit, target, ans);
            path[ip] = '-';
            dfs(ip + i + 2, id + i + 1, toAdd + toMult, -pureNum, path, digit, target, ans);
            path[ip] = '*';
            dfs(ip + i + 2, id + i + 1, toAdd, toMult * pureNum, path, digit, target, ans);
            if (pureNum == 0) break; // not allow number with 0 prefix, except zero itself;
        }
    }
}
```

利用char[]会比操作String快很多。

#### LC315. Count of Smaller Numbers After Self

这题，🙂

[LC315](https://yijiajin.github.io/leetcode/2016/12/22/Leetcode-Segment-Tree-Binary-Index-Tree/)

#### LC327. Count of Range Sum

iven an integer array `nums`, return the number of range sums that lie in `[lower, upper]` inclusive.
Range sum `S(i, j)` is defined as the sum of the elements in `nums` between indices `i` and `j` (`i` ≤ `j`), inclusive.

这题的D&C解法应该是merge的那个解法。当时不是很理解D&C，现在看看觉得这种解法其实比较好理解。

想要count符合要求的区间和，首先构造了sums数组，sums[i]代表i前所有的nums元素之和。sums[0] = 0. 这样对于数组中的每个下标***i***，假设***j***是第一个满足**sums[j] - sums[i] > upper**的数字，***k***是第一个满足**sums[k] - sums[i] >= lower**的数字，所求区间范围内的range数目为**j - k**. 对于sums的每一个元素而言都是如此，因此D&C，每次对半分成两部分。但是即使如此，要查找j, k还是要O(n), 除非sums有序，这样就可以二分查找了。所以一边对sums排序，一边查找上下边界。利用merge的方式，首先找到mid左右两部分中符合要求的range数目，这时当前范围已经排序，利用二分就可以找到当前范围中符合的range数目。O(nlgn).

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

[其他奇妙的解法](https://yijiajin.github.io/leetcode/2016/12/26/Leetcode-Binary-Search-Tree/)

感觉这次终于完全理解了这个merge的方法。所以还是要千锤百炼才行啊：D