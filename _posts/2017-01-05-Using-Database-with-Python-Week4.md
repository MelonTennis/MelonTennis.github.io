---
layout: post
title: Using Database with Python Week4／5
subtitle: Many-to-Many Relationships in SQL & Visualization
date: 2017-01-05
categories: Coursera
tags: [Database]
catalog: true
---

###  Many-to-Many Relationships in SQL & Visualization

Week4讲多对多的关系，一般而言再建一个含有两个foreign keys的table作为连接两个表的联系，通常没有primary key。 

Week5讲可视化，感觉还是learn by practice有效。

看视频感觉没讲什么…当周的题目也基本没有cover，看看[教材](http://www.pythonlearn.com/html-270/)的14.8和14.9

讲通过外键连接表格的使用模式。

> 1. reate tables with primary keys and constraints.
> 2. When we have a logical key for a person (i.e., account name) and we needthe id value for the person, depending on whether or not the person is al-ready in the People table we either need to: (1) look up the person in thePeople table and retrieve the id value for the person or (2) add the personto the People table and get the id value for the newly added row.
> 3. Insert the row that captures the “follows” relationship.

为了避免多次添加同一关系，添加**UNIQUE**字段保证唯一性。

**INSERT OR IGNORE INTO** 保证了唯一，如果存在就跳过这次INSERT。

Week4 assessment 随便搞搞，Week5 数据可视化随便搞搞assessment…

~~还没有从前两天恢复过来的样子实在是不太走心的强行学习既视感☹️~~

….






​			
​		
​	
