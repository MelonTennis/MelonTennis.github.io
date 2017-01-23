---
layout: post
title: Leetcode - Array Sum Problems
subtitle: LC1, LC15, LC18, LC16, LC259, LC454
date: 2017-01-04
categories: Leetcode
tags: [Array, TwoPointers]
catalog: true
---

## Leetcode - Array Sum Problems

无论面试成什么样我都可以期待一个奇迹...啊心好累啊QAQ  不论怎样还是强行po，顺便怒刷10+easy和medium题...果然还是hard题有意思。心好累。

### LC1. Two Sum

Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly** one solution.

O(n^2) + O(1), O(n) + O(n) 反正都挺好写。 

```java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] res = new int[2];
        HashMap<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < nums.length; i++){
            if(map.containsKey(target - nums[i])){
                res[0] = Math.min(i, map.get(target - nums[i]));
                res[1] = Math.max(i, map.get(target - nums[i]));
                return res;
            }
            // put should behind check, or if nums[i] = target/2 there will be bug
            map.put(nums[i], i);
        }
        return res;
    }
}
```

### LC15. 3Sum

Given an array *S* of *n* integers, are there elements *a*, *b*, *c* in *S* such that *a* + *b* + *c* = 0? Find all unique triplets in the array which gives the sum of zero.

**Note:** The solution set must not contain duplicate triplets.

O(n^3) + O(1), O(n^2) + O(1) 也不难

```java
public class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        int length = nums.length;
        Arrays.sort(nums);
        for(int i = 0; i < length-2; i++){
           if(i == 0 || (i > 0 && nums[i] != nums[i-1])){
               int m = i+1, n = length-1, sum = 0-nums[i];
               while(m < n){
                   if(nums[m] + nums[n] == sum){
                       res.add(Arrays.asList(nums[i], nums[m], nums[n]));
                       while(m < n && nums[m] == nums[m+1]) m++;
                       while(m < n && nums[n] == nums[n-1]) n--;
                       m++;
                       n--;
                   }else if (nums[m] + nums[n] < sum){
                       m++;
                   }else{
                       n--;
                   }
               } // while
           } // if
        } // for
        return res;
    }
}
```

### LC18. 4Sum

Given an array *S* of *n* integers, are there elements *a*, *b*, *c*, and *d* in *S* such that *a* + *b* + *c* + *d* = target? Find all unique quadruplets in the array which gives the sum of target.

O(n^3) 把3Sum改成函数就好了。

```java
public class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        int length = nums.length;
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if(length < 4 || nums == null){
            System.out.print("input");
            return res;
        }
        if(4*nums[0] > target || 4*nums[length-1] < target){
            System.out.print("not available");
            return res;
        }
        for(int i = 0; i < length-3; i++){
            if(i != 0 && nums[i] == nums[i-1])  continue;
            if(nums[i]*4 > target)  break;
            List<List<Integer>> part = threeSum(nums, i+1, target-nums[i]);
            System.out.print(part);
            for(List<Integer> list : part){
                res.add(list);
            }
        }
        return res;
        
    }
    
    public List<List<Integer>> threeSum(int[] nums, int begin, int target) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        int length = nums.length;
        for(int i = begin; i < length-2; i++){
           if(i == begin || (i > begin && nums[i] != nums[i-1])){
               int m = i+1, n = length-1, sum = target-nums[i];
               while(m < n){
                   if(nums[m] + nums[n] == sum){
                       res.add(Arrays.asList(nums[begin-1], nums[i], nums[m], nums[n]));
                       while(m < n && nums[m] == nums[m+1]) m++;
                       while(m < n && nums[n] == nums[n-1]) n--;
                       m++;
                       n--;
                   }else if (nums[m] + nums[n] < sum){
                       m++;
                   }else{
                       n--;
                   }
               } // while
           } // if
        } // for
        return res;
    }
}
```

###  LC16. 3Sum Closest

Given an array *S* of *n* integers, find three integers in *S* such that the sum is closest to a given number, target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

```
   For example, given array S = {-1 2 1 -4}, and target = 1.

    The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

和LC15差不多。O(n^2)

```java
public class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int res = 0;
        int length = nums.length;
        if(length <= 3){
            for(int n:nums) res += n;
            return res;
        } 
        for(int i = 0; i <= 2; i++) res += nums[i];
        Arrays.sort(nums);
        for(int i = 0; i < length-2; i++){
               int m = i+1, n = length-1;
               while(m < n){
                   int sum = nums[i] + nums[m] + nums[n];
                   if(sum > target) n--;
                   else if(sum < target) m++;
                   else return target;
                   if (Math.abs(sum - target) < Math.abs(res - target))  res = sum;
               } // while
        } // for
        return res;
    }
}
```

### LC259. 3Sum Smaller 

Given an array of *n* integers *nums* and a *target*, find the number of index triplets `i, j, k` with `0 <= i < j < k < n` that satisfy the condition `nums[i] + nums[j] + nums[k] < target`.

For example, given *nums* = `[-2, 0, 1, 3]`, and *target* = 2.

Return 2. Because there are two triplets which sums are less than 2:

```
[-2, 0, 1]
[-2, 0, 3]

```

**Follow up:**
Could you solve it in *O*(*n*2) runtime?

O(n^2) solution.

```java
public class Solution {
    public int threeSumSmaller(int[] nums, int target) {
        if(nums == null || nums.length < 3){
            return 0;
        }
        int length = nums.length;
        int count = 0;
        Arrays.sort(nums);
        for(int i = 0; i < length; i++) {
            int left = i + 1, right = length - 1;
            while(left < right){
                if(nums[i] + nums[left] + nums[right] < target){
                    count += right - left;
                    left++;
                }else{
                    right--;
                }
            }
        }
        return count;
    }
}
```

### LC454. 4Sum II

Given four lists A, B, C, D of integer values, compute how many tuples `(i, j, k, l)` there are such that `A[i] + B[j] + C[k] + D[l]` is zero.

To make problem a bit easier, all A, B, C, D have same length of N where 0 ≤ N ≤ 500. All integers are in the range of -228 to 228 - 1 and the result is guaranteed to be at most 231 - 1.

**Example:**

```
Input:
A = [ 1, 2]
B = [-2,-1]
C = [-1, 2]
D = [ 0, 2]

Output:
2

Explanation:
The two tuples are:
1. (0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0
```

和4Sum的区别在于从4个array而不是1个array，那就分成2个2个的就好了，因为A + B = -(C + D)和A + C = -(B + D)是完全一样的。

```java
public class Solution {
    public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
        // sum of two array, appearance times
        // A + C = -(B + D) is just the same as A + B = -(C + D), etc
        HashMap<Integer, Integer> map = new HashMap<>(); 
        for(int i = 0; i < A.length; i++){
            for(int j = 0; j < B.length; j++){
                map.put(A[i] + B[j], map.getOrDefault(A[i] + B[j], 0) + 1);
            }
        }
        int res = 0;
        for(int i = 0; i < C.length; i++){
            for(int j = 0; j < C.length; j++){
                res += map.getOrDefault(-(C[i] + D[j]), 0);
            }
        }
        return res;
    }
}
```

O(n^2), O(n^2)