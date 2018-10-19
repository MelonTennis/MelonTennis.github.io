---
layout: post
title: 遇到一个Java/Python/C++/C 类问题_(:з」∠)_
subtitle: xia xue xi
date: 2017-01-05
categories: Other
tags: [Java, Python, C/C++, GC]
catalog: true
---

### Java/Python/C++/C 一通乱学

今天面试没考编程题，考了Java/C/C++ 有什么区别， Python/Java有什么区别，~~如果我遇到了10%奇怪的数据怎么搞~~。啊好气，它们肯定是有区别的，可我一时竟然不知道怎么回答。~~(难怪有时候人家觉得转专业的除了刷题什么都不会，怎么能这么讲嘛，明明哪个专业都有啥都不会的嘛😥...)~~ 

#### Java VS. Python

> [udemy blog](https://blog.udemy.com/python-vs-java/)
>
> [中文](http://bookshadow.com/weblog/2014/05/17/python-vs-java-key-differences/)

随便举几个🌰省的没话说...

| Java                                     | Python                                   | Explain                                  |
| :--------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Static typing 静态类型                       | Dynamic typing 动态类型                      | [type mutable?](https://en.wikipedia.org/wiki/Type_system) |
| Brace 括号                                 | Indentation 缩进                           | how to seperate code block               |
| [Compiled language 编译型语言](https://en.wikipedia.org/wiki/Compiled_language) | [Interpreted language 解释型语言](https://en.wikipedia.org/wiki/Interpreted_language) | compile before running？                  |
| Portable 可移植性好                           | Open-source Libs 开源库丰富                   | ╮(╯▽╰)╭                                  |

#### Java VS. C++

感觉差别太多一时紧张竟然只想起来GC (._.) 我疯

> [wiki](https://en.wikipedia.org/wiki/Comparison_of_Java_and_C%2B%2B)
>
> [一个中文](http://xinklabi.iteye.com/blog/657334)

几个~~(我能看懂的)~~🌰：

| Java                                     | C++                                      |
| ---------------------------------------- | ---------------------------------------- |
| Object-oriented programming 面向对象程序设计     | Procedural programming 过程式程序设计+  Object-oriented programming |
| All types (primitive types and reference types) are always passed by value 基本类型传值调用 | Pointers, references, and pass-by-value are supported for all types 指针／引用／传值调用 |
| Automatic [garbage collection](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science))  自动垃圾收集(可以手动) | Manual [memory management](https://en.wikipedia.org/wiki/Memory_management) 手动存储器管理 |
| Classes are allocated on the [heap](https://en.wikipedia.org/wiki/Dynamic_memory_allocation) 在堆中分配对象的内存 | Supports classes, structs , unions, and can allocate them on the [heap](https://en.wikipedia.org/wiki/Dynamic_memory_allocation) or the [stack](https://en.wikipedia.org/wiki/Stack-based_memory_allocation). 在堆或栈分配类／结构／联合的内存 |
| Interfaces, such as Adapter, Observer, and Listener 接口 | Function pointers, function objects, lambdas, and interfaces 函数指针 & 函数对象 |

#### GC什么鬼

> [GC的七个错误认识](http://www.zicheng.net/article/90.htm)
>
> [几个相关的面试题](http://www.zicheng.net/article/61.htm)
>
> [各语言内存管理](http://hackerxu.com/2015/01/13/ram.html)



…...😳

确实觉得知识盲点太多，好多书也没看完，对这些语言也仅仅是能写写小代码，至于各种语言的特点和优势劣势，实在是不太了解。Anyway，欲速则不达，keep learning吧(._.)

喵呜。有时候会起专业基础的问题，我说需要什么我就自学balabala，毕竟technique更新很快作为技术人员本来就需要不断的学习....毕竟正经学过的VHDL/Verilog/Xmanager/乱七八糟仿真&*%^%$%^& 又怎么样呢_(:з」∠). ~~(大概是觉得忘记了有点可惜吧)~~  我什么时候也能想选OSACCDS就选OSACCDS一起搞呢... 