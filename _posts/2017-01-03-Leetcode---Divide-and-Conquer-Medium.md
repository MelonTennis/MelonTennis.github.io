---
layout: post
title: Leetcode - Divide and Conquer Medium
subtitle: LC53, LC240, LC241, LC215
date: 2017-01-03
categories: Leetcode
tags: [D&C]
catalog: true
---

### Leetcode D&C Medium Problems

最近见到几个D&C的题，一时兴起，把LC的分治法的题整理一下。希望能对分治法的理解深刻一些。觉得Blog有点丑，忍了。

#### LC53.  Maximum Subarray

Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

For example, given the array `[-2,1,-3,4,-1,2,1,-5,4]`,
the contiguous subarray `[4,-1,2,1]` has the largest sum = `6`.

这个题，当时做的时候用DP来做，实在是很基础的DP题目。DP[i]代表i为结尾的最大的子列和。O(n), O(n). 可以把空间减少到O(1).

```java
public class Solution {
    public int maxSubArray(int[] nums) {
        int[] memo = new int[nums.length];
        int max = nums[0];
        memo[0] = nums[0];
        for(int i = 1; i < nums.length; i++) {
            memo[i] = nums[i]+(memo[i-1]>0?memo[i-1]:0);
            if(memo[i]>max){
                max = memo[i];
            } // if
        } // for
        return max;
    }
}
```

**D&C solution:**

> [D&C solution](https://discuss.leetcode.com/topic/426/how-to-solve-maximum-subarray-by-using-the-divide-and-conquer-approach/2)

思路如下：O(nlgn) 解法

> 1. 得到中值，结果会包含或不包含这个值。
> 2. if(包含), 结果是左半部分包含中值的最大后缀，与右半部分的最大前缀之和
> 3. if(不包含)，对左右侧分别进行相同的D&C算法
> 4. 最终结果是2，3得到的3个结果中的最大值

```java
public class Solution {
    public int maxSubArray(int[] nums) {
        if(nums == null || nums.length == 0)    return 0;
        return helper(nums, 0, nums.length - 1);
    }
    
    private int helper(int[] nums, int left, int right){
        if(right == left)   return nums[left];
        int mid = left + (right - left)/2;
        int l = helper(nums, left, mid);
        int r = helper(nums, mid + 1, right);
        int suffix = nums[mid], prefix = nums[mid+1], temp = 0;
        for(int i = mid; i >= left; i--){
            temp += nums[i];
            suffix = Math.max(suffix, temp);
        }
        temp = 0;
        for(int i = mid + 1; i <= right; i++){
            temp += nums[i];
            prefix = Math.max(prefix, temp);
        }
        return Math.max(Math.max(l, r), prefix + suffix);
    }
}
```

#### LC240. Search a 2D Matrix II 

Write an efficient algorithm that searches for a value in an *m* x *n* matrix. This matrix has the following properties:

- Integers in each row are sorted in ascending from left to right.
- Integers in each column are sorted in ascending from top to bottom.

搜索一个矩阵是否含有某个数字，这个矩阵还是左上到右下递增排序的，那就2D Binary Search就好了吖。 

**Binary Search Solution:** 

O(m + n)

```java
public class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int row = matrix.length, col = matrix[0].length;
        if(row < 0 || col < 0) {
            return false;
        }
        int r = 0, c = col -1;
        while(r < row && c >= 0) {
            if(matrix[r][c] == target){
                return true;
            }
            else if(matrix[r][c] > target) {
                c--;
            }else{
                r++;
            }
        }
        return false;
   }
}
```

**D&C solution:**

> [D&C solution](https://discuss.leetcode.com/topic/33240/java-an-easy-to-understand-divide-and-conquer-method)

👆D&C 解法其实并没有二分搜索那么好，所以这也说明了分治法不一定是最优的喵。强行分治法，找到中值，每次可以去掉1/4的矩阵。O(n^lg3)

~~好麻烦我不实现了~~

```java
public boolean searchMatrix(int[][] matrix, int target) {
    int m = matrix.length;
    if(m<1) return false;
    int n = matrix[0].length;
    
    return searchMatrix(matrix, new int[]{0,0}, new int[]{m-1, n-1}, target);
}

private boolean searchMatrix(int[][] matrix, int[] upperLeft, int[] lowerRight, int target) {
	if(upperLeft[0]>lowerRight[0] || upperLeft[1]>lowerRight[1]
			|| lowerRight[0]>=matrix.length || lowerRight[1]>=matrix[0].length) 
		return false;
	if(lowerRight[0]-upperLeft[0]==0 && lowerRight[1]-upperLeft[1]==0)
		return matrix[upperLeft[0]][upperLeft[1]] == target;
	int rowMid = (upperLeft[0] + lowerRight[0]) >> 1;
	int colMid = (upperLeft[1] + lowerRight[1]) >> 1;
	int diff = matrix[rowMid][colMid] - target;
	if(diff > 0) {
		return searchMatrix(matrix, upperLeft, new int[]{rowMid, colMid}, target)
				|| searchMatrix(matrix, new int[]{upperLeft[0],colMid+1}, new int[]{rowMid, lowerRight[1]}, target)
				|| searchMatrix(matrix, new int[]{rowMid+1,upperLeft[1]}, new int[]{lowerRight[0], colMid}, target);
	}
	else if(diff < 0) {
 		return searchMatrix(matrix, new int[]{upperLeft[0], colMid+1}, new int[]{rowMid, lowerRight[1]}, target)
				|| searchMatrix(matrix, new int[]{rowMid+1, upperLeft[1]}, new int[]{lowerRight[0], colMid}, target)
				|| searchMatrix(matrix, new int[]{rowMid+1, colMid+1}, lowerRight, target);
	}
	else return true;
}
```

#### LC241. Different Ways to Add Parentheses

Given a string of numbers and operators, return all possible results from computing all the different possible ways to group numbers and operators. The valid operators are `+`, `-` and `*`.

**Example 1：**Input: "2-1-1".  *((2-1)-1) = 0， (2-(1-1)) = 2* Output: [0, 2]

典型分治法题目，每次遇到运算符就利用分治法分别求左右两边的结果，然后合并起来即可。O(n^3)?

```java
public class Solution {
    HashMap<String, List<Integer>> map = new HashMap<>();
    public List<Integer> diffWaysToCompute(String input) {
        // D&C
        if(map.containsKey(input)) return map.get(input);
        List<Integer> res = new ArrayList<Integer>();
        if(input == null)   return res;
        char[] arr = input.toCharArray();
        if(arr.length == 0) return res;
        for(int i = 0; i < arr.length; i++){
            if(arr[i] == '+' || arr[i] == '-' || arr[i] == '*'){
                List<Integer> left = diffWaysToCompute(input.substring(0, i));
                List<Integer> right = diffWaysToCompute(input.substring(i+1));
                for(int l: left){
                    for(int r: right){
                        switch (arr[i]){
                            case '+': res.add(l + r);
                                    break;
                            case '-': res.add(l - r);
                                    break;
                            case '*': res.add(l * r);
                                    break;
                        } // switch
                    } // for r
                } // for l 
            } // if divide
        } // for i
        // if not divide, calculate
        if(res.size() == 0){
            res.add(Integer.valueOf(input));
        }
        map.put(input, res);
        return res;
    } // diffWaysToCompute
}
```

根据discuss，如果每次把substring存到map里面再次遇到的时候直接取出结果，可以将时间减少一半以上。加了三行。

> [史蒂芬大神的一行python解法](https://discuss.leetcode.com/topic/19894/1-11-lines-python-9-lines-c/2)

#### LC215. Kth Largest Element in an Array

Find the **k**th largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

For example, Given `[3,2,1,5,6,4]` and k = 2, return 5.

今天Leetcode特别慢可能是大家都在刷题...两个月之前做的，第一眼看上去我还是好想排序。

```python
class Solution(object):
    def findKthLargest(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: int
        """
        return sorted(nums)[-k]
```

真不愧是我😂 两个月前的我果然还是排序了。O(nlgn), O(1).

什么鬼这题的意义肯定不是让我API排序的吧。👇

> [Solution explained](https://discuss.leetcode.com/topic/14597/solution-explained)

**PriorityOueue：**

优先队列，一直维护一个k大小的队列。O(nlgk), O(k)

```java
public int findKthLargest(int[] nums, int k) {

    final PriorityQueue<Integer> pq = new PriorityQueue<>();
    for(int val : nums) {
        pq.offer(val);
        if(pq.size() > k) {
            pq.poll();
        }
    }
    return pq.peek();
}
```

**Quick Select(D&C) : **

快速选择，利用分治法，跟快排的思路相似。选取枢纽值，将小于此值的放在左边，大于的放在右边, 最后返回的是枢纽所在的index。如果分界点的下标是length-k就是我们要找的，如果大于length - k就搜索左边，否则搜索右边。

O(n) best, O(n^2) worst, O(1) space.

```java
public class Solution {
    public int findKthLargest(int[] nums, int k) {
        // quick selection
        k = nums.length - k;
        int lo = 0, hi = nums.length - 1;
        while(lo < hi){
            int pivot = part(nums, lo, hi);
            if(pivot < k)   lo = pivot + 1;
            else if(pivot > k)  hi = pivot - 1;
            else break;
        }
        return nums[k];
    }
    
    private int part(int[] nums, int lo, int hi){
        int i = lo + 1, j = hi;
        while(true){
            while(i < hi && nums[i] < nums[lo]) i++;
            while(j > lo && nums[j] > nums[lo]) j--;
            if(i >= j)  break;
            exchange(nums, i, j);
            i++;
            j--;
        }
        exchange(nums, lo, j);
        return j;
    }
    
    private void exchange(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

47ms? 可是快排还只要5ms呢。Testcase全都是最差情况吗？

如果想要保证一个O(n)的时间复杂度，首先对nums进行打乱即可。

```java
private void shuffle(int a[]) {
        final Random random = new Random();
        for(int ind = 1; ind < a.length; ind++) {
            final int r = random.nextInt(ind + 1);
            exchange(a, ind, r);
        }
    }
```

加了shuffle就变成7ms了。就这样吧。



