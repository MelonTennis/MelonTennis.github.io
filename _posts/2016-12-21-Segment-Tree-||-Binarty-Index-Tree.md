---
layout: post
title: Segment Tree || Binarty Index Tree
subtitle: 线段树和树状数组
date: 2016-12-21
tags: [SegmentTree, BIT]
description: 线段树和树状数组
categories: DataStructure 
catalog: true
---

### Segment Tree 线段树

> [wiki定义](https://zh.wikipedia.org/wiki/%E7%BA%BF%E6%AE%B5%E6%A0%91)
>
> [GeeksforGeeks](http://www.geeksforgeeks.org/segment-tree-set-1-sum-of-given-range/)
>
> 线段树是一种平衡二叉树，对于每一个非叶子节点[a, b]，左子树表示区间[a, (a+b)/2], 右子树表示区间[(a+b)/2 + 1, b]。叶节点树木为N，即线段区间的长度。

基本操作：

> 建立O(n), 单点修改O(lgn), 单点查询O(lgn)*, 区间修改O(lgn)

### Binary Index Tree 树状数组

> 推荐 ✨[BIT](https://www.topcoder.com/community/data-science/data-science-tutorials/binary-indexed-trees/)
>
> [中文](http://poj.org/summerschool/1_interval_tree.pdf)
>
> 设B[ ]表示数组A[ ]的树状数组，B[i]表示A[i - 2^k]到A[i]的和。 k 表示***i***二进制表示法末尾0的个数。 👆图。-> B[i]存有***i***最后一位1所在的位置对应数量的A内元素的和。比如 8(2) = 1000, B[8] = A[1] + A[2] +…+ A[8].

基本操作：

> Sum & Update O(lgn)

 lowBit(find the 0s in tail of index):

```java
index & (-index);
// OR
index & (index ^ (index - 1));
```

Update(update A[idx]):

```java
void update(int idx, int val){
  while(idx <= N) {
    tree[idx] += val;
    idx += (idx & -idx);
  }
}
```

Sum(sum to A[idx]):

```java
int sum(int idx){
  int sum = 0;
  while(idx > 0){
    sum += tree[idx];
    idx -= idx & (-idx);
  }
  return sum;
}
```

#### 对比？

> [Compare](https://www.quora.com/How-does-one-decide-when-to-use-a-Segment-Tree-or-Fenwick-Tree)
>
> 简而言之，BIT更加灵活，需要的空间较少，适合单个元素或者连需求和情况。修改区间则效率较低。

负数的二进制表示：[wiki](https://en.wikipedia.org/wiki/Two's_complement)

2‘ complement: 1's complement + 1 补码

1's complement: 反码