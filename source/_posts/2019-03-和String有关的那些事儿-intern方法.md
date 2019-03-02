---
title: 和String有关的那些事儿-intern方法
date: 2019-03-01 16:56:50
categories: 
- Java基础
- String
tags:
- Java基础
- String
---

# 如何理解`String`的`intern`方法？

我们先来看下面这段程序：（基于JDK1.8）

```java
String str1 = "qsl";
String str2 = new String("qsl");
String str3 = new String("qsl").intern();

System.out.println(str1 == str2);//false
System.out.println(str1 == str3);//true
```



## intern()方法的定义

> Returns a canonical representation for the string object. A pool of strings, initially empty, is maintained privately by the class String. When the intern method is invoked, if the pool already contains a string equal to this String object as determined by the equals(Object) method, then the string from the pool is returned. Otherwise, this String object is added to the pool and a reference to this String object is returned. It follows that for any two strings s and t, s.intern() == t.intern() is true if and only if s.equals(t) is true. All literal strings and string-valued constant expressions are interned. String literals are defined in section 3.10.5 of the The Java? Language Specification.

上面是jdk源码对intern方法的详细解释。简单来说就是intern方法用来返回常量池中的某字符串，如果常量池中已经存在该字符串，则直接返回常量池中该对象的引用。否则，在常量池中加入该对象，然后返回引用。

**注意**：在JDK1.7之前，字符串常量池存储在方法区的永久代中。而在JDK1.8之后，字符串常量池被移到了堆中。



```java
String s3 = new String("1") + new String("1");// 此时生成了四个对象 常量池中的"1" + 2个堆中的"1" + s3指向的堆中的对象（注此时常量池不会生成"11"）
s3.intern();// jdk1.7之后，常量池不仅仅可以存储对象，还可以存储对象的引用，会直接将s3的地址存储在常量池
String s4 = "11";// jdk1.7之后，常量池中的地址其实就是s3的地址
System.out.println(s3 == s4); // jdk1.7之前false， jdk1.7之后true
```



JVM为了提高性能和减少内存开销，在实例化字符串常量的时候进行了一些优化。为了减少在JVM中创建的字符串的数量，字符串类维护了一个字符串常量池。

在JVM运行时区域的方法区中，有一块区域是运行时常量池，主要用来存储**编译期**生成的各种**字面量**和**符号引用**。

如果看过Java代码反编译后结果的同学可能知道，Java代码再便宜之后，文件结构中包含一块Constant Pool。

```java
public static void main(String[] args) {
    String str = "qsl";
}
```

经过编译以后，常量池中的内容为：

```
Constant pool:
   #1 = Methodref          #4.#20         // java/lang/Object."<init>":()V
   #2 = String             #21            // qsl
   #3 = Class              #22            // com/ccmall/learningtest/string/StringDemo1
   #4 = Class              #23            // java/lang/Object
   #5 = Utf8               <init>
   #6 = Utf8               ()V
   #7 = Utf8               Code
   #8 = Utf8               LineNumberTable
   #9 = Utf8               LocalVariableTable
  #10 = Utf8               this
  #11 = Utf8               Lcom/ccmall/learningtest/string/StringDemo1;
  #12 = Utf8               main
  #13 = Utf8               ([Ljava/lang/String;)V
  #14 = Utf8               args
  #15 = Utf8               [Ljava/lang/String;
  #16 = Utf8               str
  #17 = Utf8               Ljava/lang/String;
  #18 = Utf8               SourceFile
  #19 = Utf8               StringDemo1.java
  #20 = NameAndType        #5:#6          // "<init>":()V
  #21 = Utf8               qsl
  #22 = Utf8               com/ccmall/learningtest/string/StringDemo1
  #23 = Utf8               java/lang/Object
```

在上面的几个常量中，`str`是**符号引用**，而字面量`qsl`会被加入到常量池中，然后在类加载的过程中，这两个常量都会进入常量池。



