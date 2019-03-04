---
title: 和String有关的那些事儿-intern方法
date: 2019-01-01 16:56:50
categories: 
- Java基础
- String
tags:
- Java基础
- String
---





# 前言

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



# 运行时常量池的动态扩展



## 字符串常量池



JVM为了提高性能和减少内存开销，在实例化字符串常量的时候进行了一些优化。为了减少在JVM中创建的字符串的数量，字符串类维护了一个字符串常量池。

在JVM运行时区域的方法区中，有一块区域是运行时常量池，主要用来存储**编译期**生成的各种**字面量**和**符号引用**。

如果看过Java代码反编译后结果的同学可能知道，Java代码在编译之后，文件结构中包含一块Constant Pool。

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



## 运行时常量池

**编译期**生成的各种**字面量**和**符号引用**是运行时常量池中比较重要的一部分来源，但是并不是全部。那么还有一种情况，可以在**运行期像运行时常量池中增加常量**。那就是`String`的`intern`方法。

当一个`String`实例调用`intern()`方法时，JVM会查找常量池中是否有相同Unicode的字符串常量，如果有，则返回其的引用，如果没有，则在常量池中增加一个Unicode等于该字符串并返回它的引用。

**intern()有两个作用，第一个是将字符串字面量放入常量池（如果池没有的话），第二个是返回这个常量的引用。**



我们回过头看本篇文章开头的例子：

你可以简单的理解为`String s1 = "qsl";`和`String s3 = new String("qsl").intern();`做的事情是一样的（但实际有些区别，这里暂不展开）。都是定义一个字符串对象，然后将其字符串字面量保存在常量池中，并把这个字面量的引用返回给定义好的对象引用。

对于`String s3 = new String("qsl").intern();`，在不调`intern`情况，s3指向的是JVM在堆中创建的那个对象的引用的。但是当执行了`intern`方法时，s3将指向字符串常量池中的那个字符串常量。

由于s1和s3都是字符串常量池中的字面量的引用，所以s1==s3。但是，s2的引用是堆中的对象，所以s2!=s1。



## intern的正确用法

不知道，你有没有发现，在`String s3 = new String("qsl").intern();`中，其实`intern`是多余的？



因为就算不用`intern`，Hollis作为一个字面量也会被加载到Class文件的常量池，进而加入到运行时常量池中，为啥还要多此一举呢？到底什么场景下才会用到intern呢?



我们来看下以下代码：

```java
String s1 = "daniel";
String s2 = "qsl";
String s3 = s1 + s2;
String s4 = "daniel" + "qsl";
```
在经过反编译后，得到代码如下：

```java
String s1 = "daniel";
String s2 = "qsl";
String s3 = (new StringBuilder()).append(s1).append(s2).toString();
String s4 = "danielqsl";
```
常量池要保存的是**已确定**的字面量值。也就是说，对于字符串的拼接，纯字面量和字面量的拼接，会把拼接结果作为常量保存到字符串。

如果在字符串拼接中，有一个参数是非字面量，而是一个变量的话，整个拼接操作会被编译成`StringBuilder.append`，这种情况编译器是无法知道其确定值的。只有在运行期才能确定。

那么，有了这个特性了，`intern`就有用武之地了。**那就是很多时候，我们在程序中用到的字符串是只有在运行期才能确定的，在编译期是无法确定的，那么也就没办法在编译期被加入到常量池中。**

这时候，对于那种可能经常使用的字符串，使用`intern`进行定义，每次JVM运行到这段代码的时候，就会直接把常量池中该字面值的引用返回，这样就可以减少大量字符串对象的创建了。



如一美团点评团队的《深入解析String#intern》文中举的一个例子：

```java
static final int MAX = 1000 * 10000;
static final String[] arr = new String[MAX];

public static void main(String[] args) throws Exception {
    Integer[] DB_DATA = new Integer[10];
    Random random = new Random(10 * 10000);
    for (int i = 0; i < DB_DATA.length; i++) {
        DB_DATA[i] = random.nextInt();
    }
    for (int i = 0; i < MAX; i++) {
         //arr[i] = new String(String.valueOf(DB_DATA[i % DB_DATA.length]));
         arr[i] = new String(String.valueOf(DB_DATA[i % DB_DATA.length])).intern();
    }
}
```

在以上代码中，我们明确的知道，会有很多重复的相同的字符串产生，但是这些字符串的值都是只有在运行期才能确定的。所以，只能我们通过`intern`显示的将其加入常量池，这样可以减少很多字符串的重复创建。

上述代码经测试，加了`intern` ，程序效率会提升一半。



# 总结

new String 所谓的“如果有的话就直接引用”，指的是Java堆中创建的String对象中包含的字符串字面量直接引用字符串池中的字面量对象。也就是说，还是要在堆里面创建对象的。

而intern中说的“如果有的话就直接返回其引用”，指的是会把字面量对象的引用直接返回给定义的对象。这个过程是不会在Java堆中再创建一个String对象的。



`intern`的主要应用场景在运行时，当我们知道会有大量重复字符串产生，可以使用`intern`将其加入常量池，减少字符串的重复创建。

