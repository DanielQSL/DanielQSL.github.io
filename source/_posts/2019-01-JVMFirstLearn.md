---
title: 走进JVM-初识JVM
date: 2019-01-02 09:34:40
categories: 
- JVM虚拟机
tags:
- JVM虚拟机
---



# 什么是Java虚拟机?

Java虚拟机，是一个可以执行Java字节码的虚拟机进程。（Java源文件被编译成能被Java虚拟机执行的字节码文件）

Java 被设计成允许应用程序可以运行在任意的平台，而不需要程序员为每一个平台单独重写或者是重新编译。Java 虚拟机让这个变为可能，因为它知道底层硬件平台的指令长度和其他特性。



![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jarmvwwkj30m40f1qb9.jpg)



- 也就是说，JVM 能够跨计算机体系结构来执行 Java 字节码，主要是由于 JVM 屏蔽了与各个计算机平台相关的软件或者硬件之间的差异，使得与平台相关的耦合统一由 JVM 提供者来实现。



## JVM主要功能

1. **翻译**。JVM将字节码文件（class）转换成机器所认识的代码。由于不同的操作系统，底层的指令是不同的，JVM来帮助翻译。
2. **内存管理**。这也是JVM很重要的一个功能，后面会提到。



## JVM与普通虚拟机的区别？

- VMWare、Visual Box。必须安装操作系统，模拟了CPU指令集。
- JVM。有自己独立的运行环境：堆栈、寄存器、字节码指令。



## JVM有哪些产品？

HotSpot(Oracle)，Jrockit(Oracle)，J9(IBM)



## 为什么会出现JVM？

C、C++基于OS架构、CPU架构。通俗的举个例子，32位系统上的程序不能在64位系统上实现；没有可移植性。



## 为什么要学习JVM？

因为我们把内存管理交给了JVM，所以什么出现内存泄漏等问题，对于开发人员来说是不知情的。



# JVM由哪些部分组成？



![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jas114vaj30k30fu44e.jpg)



JVM结构由四部分组成：

- **类加载器**。在JVM启动时或者类运行时将需要的class加载到JVM中。
- **运行时数据区（内存区）**。将内存划分成若干个区以模拟实际机器上的存储、记录和调度功能模块。
- **执行引擎**。负责执行class文件中包含的字节码指令，相当于实际机器上的CPU。
- **本地方法接口**。调用 C 或 C++ 实现的本地方法的代码返回结果。



# Java运行时数据区



![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jascu83nj30ex0bojs5.jpg)

> 这张图是基于JDK6版本的运行内存的分类



- **程序计数器**。Java线程私有，指向当前线程正在执行的字节码指令的地址、行号。
- **虚拟机栈（栈内存）**。Java线程私有，存储当前线程运行方法所需要的数据、指令、返回地址。
  - 每个方法在执行的时候，都会创建一个栈帧用于存储局部变量、操作数、动态链接、方法出口等信息。
  - 每个方法调用都意味着一个栈帧在虚拟机栈中入栈到出栈的过程。
  - 局部变量表所需的内存空间在编译期间完成分配，而且分配多大的局部变量空间是完全确定的，在方法运行期间不会改变其大小。
  - 出栈后空间释放。
- **本地方法栈**。Java线程私有，和虚拟机栈的作用类似，区别是该区域为 JVM 提供使用 Native 方法的服务。
- **堆内存**。**线程共享**，存储对象或数组。垃圾收集器管理的主要区域。
  - 目前主要的垃圾回收算法都是分代收集算法，所以Java堆还可以细分为：新生代和老年代；再细致一点新生代分为Eden空间，From Survivor空间、To Survivor空间等；默认情况下，新生代按照8:1:1的比例分配。
- **方法区**。**线程共享**，用于存储虚拟机加载的类信息、常量、静态变量、即时编译器编译后（JIT）的代码等数据。
  - 运行时常量池：是方法区的一部分，用于存放编译器生成的各种字面量和符号引用。



## 后续版本的变化（主要是对方法区做了一定调整）

- JDK7的变化。
  - 存储在永久代的部分数据就已经转移到了 Java Heap 或者是 Native Heap。但永久代仍存在于 JDK7 中，但是并没完全移除。
  - 常量池和静态变量放到 Java 堆里。
- JDK8的变化。
  - 废弃 PermGen（永久代），新增 Metaspace（元数据区）。
  - 那么方法区还在么？从网上一些文章中得知：方法区在 MetaSpace 中了，方法区都是一个概念的东西。



### JDK8之后的永久代的变动

- JDK8 后用MetaSpace替代了 Perm Space ；字符串常量存放到堆内存中。
- MetaSpace 大小默认没有限制，一般根据系统内存的大小。JVM 会动态改变此值。
- 可以通过 JVM 参数配置
  - `-XX:MetaspaceSize` ： 分配给类元数据空间（以字节计）的初始大小（Oracle 逻辑存储上的初始高水位，the initial high-water-mark）。此值为估计值，MetaspaceSize 的值设置的过大会延长垃圾回收时间。垃圾回收过后，引起下一次垃圾回收的类元数据空间的大小可能会变大。
  - `-XX:MaxMetaspaceSize` ：分配给类元数据空间的最大值，超过此值就会触发Full GC 。此值默认没有限制，但应取决于系统内存的大小，JVM 会动态地改变此值。



### 为什么要废弃永久代？

1. 由于永久代内存经常不够用或发生内存泄露，爆出异常 `java.lang.OutOfMemoryError: PermGen` 。
   - 字符串存在永久代中，容易出现性能问题和内存溢出。
   - 类及方法的信息等比较难确定其大小，因此对于永久代的大小指定比较困难，太小容易出现永久代溢出，太大则容易导致老年代溢出。
2. 永久代会为 GC 带来不必要的复杂度，并且回收效率偏低。
3. Oracle 可能会将HotSpot 与 JRockit 合二为一。



# Java内存堆和栈的区别

- 栈内存用来存储基本类型的变量和对象的引用变量；堆内存用来存储Java中的对象，无论是成员变量，局部变量，还是类变量，它们指向的对象都存储在堆内存中。
- 栈内存归属于单个线程，每个线程都会有一个栈内存，其存储的变量只能在其所属线程中可见，即栈内存可以理解成线程的私有内存；堆内存中的对象对所有线程可见。堆内存中的对象可以被所有线程访问。
- 如果栈内存没有可用的空间存储方法调用和局部变量，JVM 会抛出 `java.lang.StackOverFlowError` 错误；如果是堆内存没有可用的空间存储生成的对象，JVM 会抛出 `java.lang.OutOfMemoryError` 错误。
- 栈的内存要远远小于堆内存，如果你使用递归的话，那么你的栈很快就会充满。`-Xss` 选项设置栈内存的大小，`-Xms` 选项可以设置堆的开始时的大小。



![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jasq7b2lj30ht09eq38.jpg)



**总结**：JVM 中堆和栈属于不同的内存区域，使用目的也不同。栈常用于保存方法帧和局部变量，而对象总是在堆上分配。栈通常都比堆小，也不会在多个线程之间共享，而堆被整个 JVM 的所有线程共享。





