---
layout: post
title: Divide and Conquer
subtitle: D&C 个人理解
date: 2017-01-03
categories: DataStructure
tags: [D&C]
catalog: true
---

### Divide and Conquer 分治法

近来刷LC，看到好多题要用分治法来解，感觉理解的不是很好，故特此学习。

> [Wiki](https://en.wikipedia.org/wiki/Divide_and_conquer_algorithm)

##### 概念：

分治法，将一个较难处理的问题分成一系列子问题，分别解决这些子问题，直到子问题可以简单的求解。原问题的解可以认为是子问题解的合并。一般而言这些子问题是**没有交集**的并且具有**最优子结构**的性质。

> 分治法的三个步骤是：
>
> 1. 分解（Divide）：将原问题分解为若干子问题，这些子问题都是原问题规模较小的实例。
> 2. 解决（Conquer）：**递归**地求解各子问题。如果子问题规模足够小，则直接求解。
> 3. 合并（Combine）：将所有子问题的解合并为原问题的解

> **Examples: **
>
> - Merge Sort
> - Quick Sort
> - Binary Search

看来分治法已经接触了一些，但是一直没有理解分治法的奥义啊 QAQ

##### 实现：

* Recursion 递归

  Recursive case 递归情况／ Base case 基本情况

  三种求递归式的解法：代入／递归树／主方法

* Stack／queue/ PriorityQueue

* Base cases

* Sharing repeated subproblems

~~LC上分治法的题，真是各有各的难度(笑)~~

##### 运行时间

![](/plot/plot1.png)

![](/plot/plot2.png)