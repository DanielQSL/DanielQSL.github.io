---
title: 理解synchronized的实现原理
date: 2019-03-06 15:14:46
categories: 
- JVM并发
tags:
- JVM并发
- synchronized
---



# 实现原理

> synchronized`可以保证方法或者代码块在运行时，同一时刻只有一个方法可以进入到临界区，同时它还可以保证共享变量内存可见性。



Java 中每一个对象都可以作为锁，这是 `synchronized` 实现同步的基础：

1. 普通同步方法，锁是当前实例对象
2. 静态同步方法，锁是当前类的 class 对象
3. 同步方法块，锁是括号里面的对象

PS:1）同步代码块是使用`monitorenter`和`monitorexit`指令来实现的。2）同步方法依靠的是方法修饰符上的`ACC_SYNCHRONIZED`实现。



任何对象都可以成为锁，那么锁的信息又存在在内存的什么地方呢？下面介绍两个重要的概念：Java对象头、Monitor。



# Java对象头