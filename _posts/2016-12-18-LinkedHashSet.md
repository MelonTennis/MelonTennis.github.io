---
layout: post
title: LinkedHashSet | HashSet 
subtitle: Compare LinkedHashSet and HashSet
date: 2016-12-18
categories: blog
tags: [java, hashset, linkedhashset]
description: LinkedHashSet & HashSet 
catalog: true
---
### HashSet 和 LinkedHashSet 

第一次接触到LinkedHashSet, LC381.Insert Delete GetRandom O(1) - Duplicates allowed
如果用LinkedHashSet 150ms， 用HashSet就要 200ms... 

### 参考
> [HashSet, TreeSet 和 LinkedHashSet区别](http://www.cnblogs.com/Terry-greener/archive/2011/12/02/2271707.html)

> [HashSet VS TreeSet VS LinkedHashSet](http://www.programcreek.com/2013/03/hashset-vs-treeset-vs-linkedhashset/)

> [java 集合](http://liuzxc.github.io/blog/java-advance-05/)

> [Difference between HashSet, TreeSet and LinkedHashSet](http://www.java67.com/2014/01/when-to-use-linkedhashset-vs-treeset-vs-hashset-java.html)

总体看来LinkedHashSet 和 HashSet的区别在于加入时前者是按照链表的顺序排列的， 而hashset则不一定按照加入顺序排列。另外对于iterator().next()，LinkedHashSet更快一些。

> According to Javadoc
> Iterating over this set requires time proportional to the sum of the HashSet instance's size (the number of elements) plus the "capacity" of the backing HashMap instance (the number of buckets).The idea is that, when iterator() is called, HashSet needs to link the its elements while LinkedHashSet has the linkage ready. Therefore, HashSet's iterator() might cost O(n) .

