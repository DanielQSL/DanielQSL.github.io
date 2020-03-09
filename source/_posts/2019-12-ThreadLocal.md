---
title: 实现多线程的正确姿势-线程池
date: 2019-12-01 17:32:40
categories: 
- Java多线程
tags:
- 线程池

---



# ThreadLocal两大使用场景

1. 每个线程内需要保存全局变量（例如拦截器中获取用户信息），可以让不同方法直接使用，避免参数传递。
   - 用ThreadLocal保存一些业务内容（用户权限信息、从用户系统获取的UserID等）
   - 这些信息在同一个线程内相同，但是不同的线程使用的业务内是不相同的
2. 每个线程需要独享的对象（通常是工具类，典型需要使用的类有SimpleDateFormat和Random）



# ThreadLocal特点

- 强调的是**同一个请求内（同一个线程内）不同方法间的共享**
- 不需要重写initialVlaue()方法，但是必须手动调用set()方法



# ThreadLocal两个作用

1. 让某个需要用到的对象在线程间隔离（每个线程都有自己的独立的对象）
2. 在任何方法中都可以轻松获取到该对象



# ThreadLocal的好处

- 达到线程安全
- 不需要加锁，提高执行效率
- 更高效地利用好内存、节省开销
- 免去传参的繁琐



# ThreadLocal原理

弄清楚Thread、ThreadLocal以及ThreadLocalMap三者的关系

![666666.png](http://ww1.sinaimg.cn/large/741d029bgy1gco29lv2zqj20h20f0wif.jpg)





# ThreadLocal使用注意点

- 内存泄露
  - 某个对象不再使用，但是占用内存却不能被回收
  - key的泄露：ThreadLocalMap中得Entry继承自WeakReference，是弱引用
  - value的泄露：每个entry都包含一个队value的强引用
- 如何避免（阿里规约）
  - 使用完ThreadLocal之后，调用remove方法，就会删除对应的entry对象，可以避免内存泄露
- 空指针异常
- 如果set进去的东西本来就是多线程共享的同一个对象，如static对象，那么还是会有并发访问问题