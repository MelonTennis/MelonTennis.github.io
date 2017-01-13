---
layout: post
title: Leetcode - Permutation problems
subtitle: LC31, LC46, LC47, LC60, LC266, LC267
date: 2017-01-10
categories: Leetcode
tags: [Backtracking]
catalog: true
---

#### LC31. Next Permutation

Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers.

If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).

The replacement must be in-place, do not allocate extra memory.

`1,2,3` → `1,3,2`
`3,2,1` → `1,2,3`
`1,1,5` → `1,5,1`

##### Naive solution：

找到第一个比后面小的数字，取出第一个比他大的数并排列其他。。。用冒泡排序的话好像恰好是最差情况(._.)

```java
public class Solution {
    public void nextPermutation(int[] nums) {
        for(int i = nums.length - 1; i > 0; i--){
            if(nums[i - 1] < nums[i]){
                sort(nums, i-1, nums[i - 1]);
                return;
            }
        }
        Arrays.sort(nums);
        return;
    }
    
    private void swap(int[] nums, int a, int b){
        int t = nums[a];
        nums[a] = nums[b];
        nums[b] = t;
    }
    
    private void sort(int[] nums, int begin, int cur){
        for(int i = begin; i < nums.length - 1; i++){
            for(int j = begin; j < nums.length - 1 - i + begin; j++) {
                if(nums[j] > nums[j + 1])   swap(nums, j, j+1);
            }
        }
        System.out.println(Arrays.toString(nums));
        int p = begin;
        while(p < nums.length && nums[p] <= cur) p++;
        System.out.println(p);
        int temp = nums[p];
        for(int i = p; i > begin; i--){
            nums[i] = nums[i - 1];
        }
        System.out.println(Arrays.toString(nums));
        nums[begin] = temp;
    }
}
```

O(n^2), 43ms

##### Better solution:

> [Java solution](https://discuss.leetcode.com/topic/30212/easiest-java-solution)

找到第一个打破递减顺序的数字后，找到比他大的第一个数字，交换，然后将后续的数字翻转，顺序就变成递增的了。

```java
public class Solution {
    public void nextPermutation(int[] A) {
     if(A == null || A.length <= 1) return;
    int i = A.length - 2;
    while(i >= 0 && A[i] >= A[i + 1]) i--; // Find 1st id i that breaks descending order
    if(i >= 0) {                           // If not entirely descending
        int j = A.length - 1;              // Start from the end
        while(A[j] <= A[i]) j--;           // Find rightmost first larger id j
        swap(A, i, j);                     // Switch i and j
    }
    reverse(A, i + 1, A.length - 1);       // Reverse the descending sequence
}

public void swap(int[] A, int i, int j) {
    int tmp = A[i];
    A[i] = A[j];
    A[j] = tmp;
}

public void reverse(int[] A, int i, int j) {
    while(i < j) swap(A, i++, j--);
}
}
```

O(n), 20ms

#### LC46. Permutations

Given a collection of **distinct** numbers, return all possible permutations.

一个经典的回溯题。

```java
public class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        List<Integer> cur = new ArrayList<Integer>();
     //   if(nums == null || nums.length == 0)    return res;
        helper(cur, nums, res);
        return res;
    }
    
    private void helper(List<Integer> cur, int[] nums, List<List<Integer>> res){
        if(cur.size() == nums.length){
            res.add(new ArrayList<Integer>(cur));
            return;
        }
        for(int n: nums){
            if(!cur.contains(n)){
                cur.add(n);
                helper(cur, nums, res);
                // here: list.remove(int index) VS. list.remove(Object o)
                cur.remove(cur.size() - 1);
            }
        }
        return;
    }
}
```

⚠️：list.remove(int index) 和 list.remove(Object o)的区别，如果是ArrayList\<Integer>, 要将int转化为Integer才能remove(Object).

O(n^n) ...😰

#### LC47. Permutations II

Given a collection of numbers that might contain duplicates, return all possible unique permutations.

存在duplicate，当前一个duplicate没有被使用时会产生重复情况。所以首先排序，然后当前一个duplicate被使用时加入后一个即可。用array表示visit会快一些。

> [easy solution](https://discuss.leetcode.com/topic/31445/really-easy-java-solution-much-easier-than-the-solutions-with-very-high-vote/3)

```java
public class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
       
      List<List<Integer>> res = new ArrayList<List<Integer>>();
        if(nums==null || nums.length==0) return res;
        boolean[] used = new boolean[nums.length];
        List<Integer> list = new ArrayList<Integer>();
        Arrays.sort(nums);
        dfs(nums, used, list, res);
        return res;
    }

    public void dfs(int[] nums, boolean[] used, List<Integer> list, List<List<Integer>> res){
        if(list.size()==nums.length){
            res.add(new ArrayList<Integer>(list));
            return;
        }
        for(int i=0;i<nums.length;i++){
            if(used[i]) continue;
            if(i>0 &&nums[i-1]==nums[i] && !used[i-1]) continue;
            used[i]=true;
            list.add(nums[i]);
            dfs(nums,used,list,res);
            used[i]=false;
            list.remove(list.size()-1);
        }
    }
}
```

O(n^n) …🤔 

#### LC60. Permutation Sequence

The set `[1,2,3,…,*n*]` contains a total of *n*! unique permutations.

By listing and labeling all of the permutations in order,
We get the following sequence (ie, for *n* = 3):

1. `"123"`
2. `"132"`
3. `"213"`
4. `"231"`
5. `"312"`
6. `"321"`

Given *n* and *k*, return the *k*th permutation sequence.

> [iterative solution](https://discuss.leetcode.com/topic/5081/an-iterative-solution-for-reference/2)

首先分析数组的构成，n! 可以分成n\*(n-1)!, 每次都如此分解，直到最后只有1个可能。每次的第一位下标就是k/(n-1)!， 对于下一位k = k%(n-1)! 

```java
public class Solution {
    public String getPermutation(int n, int k) {
       LinkedList<Integer> list = new LinkedList<>();
       int factor = 1;
       for(int i = 0; i < n; i++){
           list.add(i + 1);
           factor *= i + 1;
       }
       StringBuilder sb = new StringBuilder();
       k--;
       for(; n > 0; n--){
           factor /= n;
           sb.append(list.remove(k/factor));
           k %= factor;
       }
       return sb.toString();
    }
}
```

O(n), O(1)

#### LC266. Palindrome Permutation

Given a string, determine if a permutation of the string could form a palindrome.

```java
public class Solution {
    public boolean canPermutePalindrome(String s) {
        if(s == null)   return false;
        char[] arr = s.toCharArray();
        int[] count = new int[256];
        for(char c: arr){
            count[(int)c]++;
        }
        int num = 0;
        for(int i = 0; i < 256; i++){
            if(count[i] % 2 != 0)   num++;
            if(num > 1) return false;
        }
        return true;
    }
}
```

O(n)

#### LC267. Palindrome Permutation II

Given a string `s`, return all the palindromic permutations (without duplicates) of it. Return an empty list if no palindromic permutation could be form.

For example:

Given `s = "aabb"`, return `["abba", "baab"]`.

Given `s = "abc"`, return `[]`.

先判断是否能构成回文，然后取出一半得到所有排列，最后构成回文序列。

```java
public class Solution {
    public List<String> generatePalindromes(String s) {
        List<String> res = new ArrayList<String>();
        char[] arr = s.toCharArray();
        int[] count = new int[256];
        boolean flag = false;
        char extra = ' ';
        List<Character> half = new ArrayList<>();
        for(char c: arr)    count[(int)c]++;
        for(int i = 0; i < 256; i++){
            if(count[i] %2 != 0){
                if(flag)    return res;
                flag = true;
                extra = (char)i;
            }
            count[i] = count[i]/2;
            for(int j = 0; j < count[i]; j++)   half.add((char)i);
        } // for
        //System.out.println(half);
        boolean[] visit = new boolean[half.size()];
        StringBuilder sb = new StringBuilder();
        helper(res, sb, half, extra, visit);
        return res;
    }
    
    private void helper(List<String> res, StringBuilder sb, List<Character> half, char extra, boolean[] visit){
        if(sb.length() == half.size()){
            String cur = sb.toString() + (extra==' '?"":extra) + sb.reverse().toString();
            res.add(cur);
            // reverse sb to origional here!
            sb.reverse();
            return;
        }
        for(int i = 0; i < half.size(); i++){
            if(visit[i])    continue;
            if(i > 0 && half.get(i) == half.get(i-1) && !visit[i-1])    continue;
            visit[i] = true;
            sb.append(half.get(i));
            helper(res, sb, half, extra, visit);
            sb.deleteCharAt(sb.length() - 1);
            visit[i] = false;
        }
        return;
    }
}
```

O(n^n) 我算错了吗…? 还是回溯的时间都这么可怕😨