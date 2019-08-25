---
layout: post
title: Leetcode - House Robber / Subarray Product
subtitle: LC198, LC213, LC337, LC740, LC152, LC713
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## House Robber

Leetcode DP tag下的一个抢劫系列问题。解决方法是1d动态规划。

### House Robber

抢劫，不能连续抢两个挨着的房子，求最大收益。两个状态，抢，不抢。O(N), O(1).

```java
class Solution {
    public int rob(int[] nums) {
        if(nums == null || nums.length == 0)    return 0;
        int[] dp = new int[2];
        for(int i = 0; i < nums.length; i++) {
            int[] next = new int[2];
            next[0] = dp[1] + nums[i];
            next[1] = Math.max(dp[1], dp[0]);
            dp = next;
        }
        return Math.max(dp[0], dp[1]);
    }
}
```



### House Robber II

在I的基础上，房子连成了一个圈，首尾不可以同时抢。同样的对于每一个房子都有抢，不抢两种状态，来自于上一个房子的状态。对于某个房子，如果不抢，那么就可以break掉这个圆。所以这个问题可以转换为对于房子i，一定不抢房子i产生的问题I的解和一定不抢房子i + 1产生的问题I的解其中的最大值。为了方便取i为0。O(N), O(1).

```java
class Solution {
    public int rob(int[] nums) {
        if(nums == null || nums.length == 0)    return 0;
        if(nums.length == 1)    return nums[0];
        return Math.max(rob(nums, 0, nums.length - 1), rob(nums, 1, nums.length));
    }
    
    private int rob(int[] nums, int start, int end) {
        if(end - start == 0)    return 0;
        int[] dp = new int[2];
        for(int i = start; i < end; i++) {
            int[] next = new int[2];
            next[0] = dp[1] + nums[i];
            next[1] = Math.max(dp[1], dp[0]);
            dp = next;
        }
        return Math.max(dp[0], dp[1]);
    }
}
```



### House Robber III

在I的基础上，所有的房子排成了树状，只能从root进入。这里是dfs了，对于每一个子树都返回抢，不抢两种状态的最大值，然后依次更新parents直到root。O(N), O(1).

```java
public class Solution {
    public int rob(TreeNode root) {
        int[] result = roob(root);
        return Math.max(result[0], result[1]);
        }

    public int[] roob(TreeNode root){
        int[] num = new int[2];
        if(root == null) {
            return num;
        }
        int[] left = roob(root.left);
        int[] right = roob(root.right);
        num[0] = root.val+left[1]+right[1];
        num[1] = Math.max(left[0], left[1])+Math.max(right[0], right[1]);
        return num;
    } 
}
```



### Delete and Earn

取一个数就一定要删去**每一个**相邻的数，求能取到数的和最大是多少。其实就是house robber的相邻房间抢与不抢问题变化一下，需要先排序再判断是不是相邻的。O(NlgN), O(1).

```java
class Solution {
    public int deleteAndEarn(int[] nums) {
        if(nums == null || nums.length == 0)    return 0;
        Arrays.sort(nums);
        int n = nums.length, skip = 0, keep = 0, i = 0, pre = 10001;
        while(i < n) {
            int keepi = 0;
            int skipi = Math.max(skip, keep);
            if(pre == nums[i] - 1) {
                keepi = skip;
            } else {
                keepi = Math.max(skip, keep);
            }
            int j = i;
            while(j < n && nums[j] == nums[i]) {
                keepi += nums[j];
                j++;
            }
            pre = nums[i];
            i = j;
            skip = skipi;
            keep = keepi;
        }
        return Math.max(skip, keep);
    }
}
```



## Subarray Product

Leetcode DP下的subarray求积问题，明确状态转换方程就很简单。

### Maximum Product Subarray

找到数组中product最大的subarray。这里subarray一定是连续的，所以当前的结果只取决于前一个数的状态，是1d的DP问题。对于每个数，有最大值和最小值两种状态。O(N), O(1).

```java
class Solution {
    public int maxProduct(int[] nums) {
        if(nums == null || nums.length == 0)    return 0;
        int n = nums.length, curMax = 1, curMin = 1, max = nums[0];
        for(int i = 0; i < n; i++) {
            if(nums[i] >= 0) {
                curMax = Math.max(nums[i], nums[i] * curMax);
                curMin = Math.min(nums[i], nums[i] * curMin);
                max = Math.max(curMax, max);
            } else {
                int preMax = curMax;
                curMax = Math.max(nums[i], nums[i] * curMin);
                curMin = Math.min(nums[i], nums[i] * preMax);
                max = Math.max(max, curMax);
            }
        }
        return max;
    }
}
```



### Subarray Product Less Than K

和上面一题不一样的是，这里所有的元素都是正数并且希望得到乘积小于K的subarray个数。求个数问题只要在每一个element的状态都更新res变量，这里由于不仅求极值，所以不仅是和前一个element相关，变成了2d的2 pointer问题。O(N*N), O(1).

```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        if(nums == null)    return 0;
        int i = 0, j = 0, n = nums.length, pre = 1, res = 0;
        for(i = 0, j = 0; j < n; j++) {
            pre *= nums[j];
            while(pre >= k && i <= j) {
                pre /= nums[i++];
            }
            res += j - i + 1;
        }
        return res;
    }
}
```