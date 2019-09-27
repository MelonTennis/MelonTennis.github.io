---
layout: post
title: Singleton
subtitle: 
date: 2019-09-02
categories: Other
tags: []
description: 
catalog: true
---

## Singleton 单例模式

准备面试ing。我觉得这大概就是我为数不多的能够做sde的机会了。加油啊。求人品。求走运。

先复习一下[Java的访问权限和关键字](https://melontennis.github.io/other/2017/01/06/访问权限-&-关键字/)。

[Singleton Pattern](https://en.wikipedia.org/wiki/Singleton_pattern),  a class is restricted to have one single instance. 

代码参考了[如何正确的写出单例模式](http://wuchong.me/blog/2014/08/28/how-to-correctly-write-singleton-pattern/)和[Singleton Design Pattern Implementation](https://www.geeksforgeeks.org/singleton-design-pattern/).

### Lazy 懒汉式

```java
public class Singleton {
  private static Singleton instance;
  private Singleton(){}
  
  public static Singleton getInstance() {
    if(instance == null) {
			instance = new Singleton();
    }
    return instance;
  }
}
```

很明显，多线程条件下会创建多个实例。那么加一个synchronized就可以了。

```java
public class Singleton {
  private static Singleton instance;
  private Singleton(){}
  
  public static synchronized Singleton getInstance() {
    if(instance == null) {
			instance = new Singleton();
    }
    return instance;
  }
}
```

但是同步只有第一次生成实力才需要，getInstance()每次只有一个线程能够调用。

### Eager Instantiation 饿汉式

```java
public class Singlenton {
  private static Singlenton instance = new Singleton();
  private Singlenton(){}
  
  public static Singleton getInstance() {
    return instance;
  }
}
```

第一次加载类时初始化，是线程安全的。Use this method only when your singleton class is light and is used throughout the execution of your program.

### Double Checked Locking 双重检验锁

可能有很多线程进入synchronized外的if，因此需要两次检验。

We have declared the obj [volatile](https://www.geeksforgeeks.org/volatile-keyword-in-java/) which ensures that multiple threads offer the obj variable correctly when it is being initialized to Singleton instance. This method drastically reduces the overhead of calling the synchronized method every time.

```java
public class Singleton {
  private volatile static Singleton instance;
  private Singleton(){}
  
  public static Singleton getInstance() {
    if(instance == null) {
      synchronized(Singleton.class) {
        if(instance == null) {
          instance = new Singleton();
        }
      }
    }
    return instance;
  }
}
```

volatile保证了变量的赋值后面有barrier，读不会重排序到barrier之前。



行我就知道这些吧。