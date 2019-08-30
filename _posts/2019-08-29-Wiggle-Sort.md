---
layout: post
title: Leetcode Wiggle Sort
subtitle: 
date: 2019-08-29
categories: Leetcode
tags: [Sort]
description: 
catalog: true
---

## Wiggle Sort 

### 280. Wiggle Sort

Given an unsorted array `nums`, reorder it **in-place** such that `nums[0] <= nums[1] >= nums[2] <= nums[3]...`

此处可以取等，简单的交换就可以解决。O(n), O(1).

```java
class Solution {
    public void wiggleSort(int[] nums) {
        for(int i = 0; i < nums.length - 1; i++) {
            if((i % 2 == 0) != (nums[i] < nums[i + 1])) {
                swap(nums, i, i + 1);
            }
        }
        return;
    }
    
    private void swap(int[] nums, int i, int j) {
        int t = nums[i];
        nums[i] = nums[j];
        nums[j] = t;
    }
}
```



### 324. Wiggle Sort II

Given an unsorted array `nums`, reorder it such that `nums[0] < nums[1] > nums[2] < nums[3]...`

此时不可以取等，简单交换不可以解决。因为不可以取等，所以可以排序并且分成前后两部分，前面取一个后面取一个。O(NlgN). O(N).

```java
class Solution {
    public void wiggleSort(int[] nums) {
        if(nums == null || nums.length == 0)    return;
        int n = nums.length;
        int[] tmp = nums.clone();
        Arrays.sort(tmp);
        int i = (n + 1) / 2 - 1, j = n - 1;
        for(int k = 0; k < n; k++) {
            nums[k] = (k % 2 == 0)? tmp[i--]:tmp[j--];
        }
        return;
    }
}
```

这题的followup是O(n) time in place. 就比较难想了。

[Discuss](<https://leetcode.com/problems/wiggle-sort-ii/discuss/77684/Summary-of-the-various-solutions-to-Wiggle-Sort-for-your-reference>)

我们想要找到前半部分和后半部分，但是每一个半部分不需要是排序的。找到数组的前k/第k个数，是 [LC215]()这一题。为了防止中位数有很多重复而取等，将数组分成小于中位数，等于中位数，大于中位数三个部分。这个过程可以通过O(N), O(N)来完成。

```java
public void wiggleSort(int[] nums) {
    int n = nums.length, m = (n + 1) >> 1;
    int[] copy = Arrays.copyOf(nums, n);
    int median = kthSmallestNumber(nums, m);
    
    for (int i = 0, j = 0, k = n - 1; j <= k;) {
        if (copy[j] < median) {
            swap(copy, i++, j++);
        } else if (copy[j] > median) {
            swap(copy, j, k--);
        } else {
            j++;
        }
    }
        
    for (int i = m - 1, j = 0; i >= 0; i--, j += 2) nums[j] = copy[i];
    for (int i = n - 1, j = 1; i >= m; i--, j += 2) nums[j] = copy[i];
}

private int kthSmallestNumber(int[] nums, int k) {
    Random random = new Random();
    
    for (int i = nums.length - 1; i >= 0; i--) {
        swap(nums, i, random.nextInt(i + 1));
    }
    
    int l = 0, r = nums.length - 1;
    k--;
        
    while (l < r) {
        int m = getMiddle(nums, l, r);
        
        if (m < k) {
            l = m + 1;
        } else if (m > k) {
            r = m - 1;
        } else {
            break;
        }
    }
    
    return nums[k];
}
    
private int getMiddle(int[] nums, int l, int r) {
    int i = l;
    
    for (int j = l + 1; j <= r; j++) {
        if (nums[j] < nums[l]) swap(nums, ++i, j);
    }
    
    swap(nums, l, i);
    return i;
}

private void swap(int[] nums, int i, int j) {
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```

但是想要O(1), 只能inplace划分数组。这里的想法是当我们找到了三个部分之后不需要放在另一个数组里，而是直接放在需要的位置上，通过一种index mapping的映射。(1 + 2 * index) % (n | 1) [这](<https://leetcode.com/problems/wiggle-sort-ii/discuss/77678/3-lines-python-with-explanation-proof>)是一种我想不出来的解法。

[Discuss](<https://leetcode.com/problems/wiggle-sort-ii/discuss/77682/step-by-step-explanation-of-index-mapping-in-java>) 

```java
 public void wiggleSort(int[] nums) {
        int median = findKthLargest(nums, (nums.length + 1) / 2);
        int n = nums.length;

        int left = 0, i = 0, right = n - 1;

        while (i <= right) {

            if (nums[newIndex(i,n)] > median) {
                swap(nums, newIndex(left++,n), newIndex(i++,n));
            }
            else if (nums[newIndex(i,n)] < median) {
                swap(nums, newIndex(right--,n), newIndex(i,n));
            }
            else {
                i++;
            }
        }


    }

    private int newIndex(int index, int n) {
        return (1 + 2*index) % (n | 1);
    }
```

当然也可简单一点，隔一个放一个数进去。为了防止相等的数相邻，前半部分从前向后+2放，后半部分从后向前-2放。

```java
class Solution {
    public void wiggleSort(int[] nums) {
        // quickselect find median and swap    
        int  n = nums.length, l = 0, r = n - 1;
        int pivot = find(nums, l, r);
        int k = n / 2;
        while(pivot != k) {
            if(pivot < k) {
                l = pivot + 1;
            } else {
                r = pivot - 1;
            }
            pivot = find(nums, l, r);
        }
        
        //i: odd < j: even 
        int i = 1, j = (n - 1) / 2 * 2;	// max even index
        int x = j, med = nums[pivot];
        
        for(k = 0; k < n; k++){
            if(nums[x] > med){
                swap(nums, x, i);
                i += 2;
            } else if(nums[x] < med){
                swap(nums, x, j);
                j = j - 2;
                x = x - 2;
                if(x < 0) x = n / 2 * 2 - 1;	// max odd index
            } else {
               x = x - 2;
               if(x < 0) x = n / 2 * 2 - 1;
            }
        }
    }
    
    private int find(int[] nums, int l, int r) {
        int p = l + (r - l) / 2;
        swap(nums, p, r);
        int idx = l;
        while(idx < r) {
            if(nums[idx] < nums[r]) {
                swap(nums, l++, idx);
            }
            idx++;
        }
        swap(nums, l, idx);
        return l;
    }
    
    private void swap(int[] nums, int p, int q) {
        int t = nums[p];
        nums[p] = nums[q];
        nums[q] = t;
        return;
    }
}
```

