---
layout: post
title: Leetcode - Rotated Array
subtitle: Find or Search in rotated array
date: 2016-12-22
categories: Leetcode
tags: [Array, BinarySearch]
catalog: true
---

### LC33. Search in Rotated Sorted Array

You are given a target value to search. If found in the array return its index, otherwise return -1. No duplicates.

典型的binary search问题，先找到哪边是升序排列的binary search，否则就另一半查找。

> Reference: [Discuss](https://discuss.leetcode.com/topic/16580/java-ac-solution-using-once-binary-search)

O(lgn)      ⚠️：>= <= > <  

```java
public class Solution {
    public int search(int[] nums, int target) {
        // binary search 
        int low = 0, high = nums.length-1;
        while(low <= high){
            int mid = low + (high - low)/2;
            if(nums[mid] == target) {
                return mid;
            }
            // left part is in order 
            // don't miss equals, that means our searching is about to end
            if(nums[mid] >=  nums[low]){
                if(nums[mid] > target && target >= nums[low]){
                    high = mid - 1;
                }else{
                    low = mid + 1;
                }
            }
            // right part is in order
            // don't miss equals, that means our searching is about to end
            if(nums[mid] <= nums[high]){
                if(nums[mid] < target && target <= nums[high]){
                    low = mid + 1;
                }else{
                    high = mid - 1;
                }
            }
        } // while
        return -1;
    }
}
```

###  LC33. Search in Rotated Sorted Array II

Duplicate exists. Binary search like above, but worst O(n).

nums[mid]与nums[begin]比较，有三种情况， 分别对应【mid-begin】sorted，【mid-end】sorted, 如果相等就low++, 直到begin = end。

```java
public class Solution {
    public boolean search(int[] nums, int target) {
        int length = nums.length;
        int begin = 0, end = length-1;
        while(begin < end){
            int mid = begin+(-begin + end)/2;
            if(nums[mid] == target) {
                return true;
            }
            // left sorted
            if (nums[mid] > nums[begin]){
                if(nums[begin] <= target && target < nums[mid]) {
                    end = mid - 1;
                }else{
                    begin = mid + 1;
                }
            }else if(nums[mid] < nums[begin]){ // right sorted
                if(nums[mid] < target && nums[end] >= target){
                    begin = mid +1;
                }else{
                    end = mid-1;
                }
            }else{ // duplicate
                begin++;
            }
        } // while
        if(nums[begin] == target)   return true;
        return false;
    }
}
```

### LC153. Find Minimum in Rotated Sorted Array

Find min, no duplicates. 

Binary Search, find the min in left and right respectively, and return the min of two.

O(lgn)

```java
public class Solution {
    public int findMin(int[] nums) {
        int length = nums.length;
        if(length == 1) {
            return nums[0];
        }
        int begin = 0; 
        int end = length-1;
        int left = nums[begin], right = nums[end];
        while(begin <= end) {
            int mid = begin + (end-begin)/2;
            if(nums[mid] > nums[begin]) { // left sorted
                left = Math.min(nums[begin], left);
                begin = mid+1;
                continue;
            }
            if(nums[mid] < nums[begin]) { // right sorted
                right = Math.min(right, nums[mid]);
                end = mid-1;
                continue;
            }
            if(nums[mid] == nums[begin]){ // end of searching
                left = Math.min(nums[begin], left);
                right = Math.min(right, nums[end]);
                break;
            }
        } // while
        return Math.min(left, right);
    }
}
```

### LC154. Find Minimum in Rotated Sorted Array II

Duplicate allowed. Worst O(n). 

```java
public class Solution {
    public int findMin(int[] nums) {
        // binary search
        // rotate array means ir is partial ascending
        int low = 0, high = nums.length-1;
        while(low <= high){
            int mid = low + (high - low)/2;
            if(nums[mid] > nums[high]){ // lowest is int right side
                low = mid + 1;
            }else if(nums[mid] < nums[high]){ // lowest in the left side
                high = mid; // mid may be min
            }else{
                high--;
            }
        } // while
        return nums[low];
    }
}
```

