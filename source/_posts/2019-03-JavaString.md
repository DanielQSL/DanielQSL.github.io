---
title: 和String有关的那些事儿
date: 2019-01-01 11:18:25
categories: 
- Java基础
tags:
- Java基础
- String
---



# 1.如何比较两个字符串？使用“==”还是equals()方法？

答：`==`比较的是两个对象的引用是否相同，而equals()比较的是两个字符串的值是否相等。除非你想检查的是两个字符串是否是同一个对象，否则你应该使用equals()来比较字符串。



# 2.`String str = new String("qsl");`定义了几个对象？

答：若常量池中已经存在`qsl`，则直接引用，此时只会创建一个对象；如果常量池中不存在`qsl`，则会先创建后引用，也就是两个对象。



**画外音**

我们下来看下面这段代码的执行结果：

```java
public class StringDemo {
    public static void main(String[] args) {
        String str1 = "qsl";
        String str2 = "qsl";
        String str3 = new String("qsl");

        System.out.println(str1 == str2);//true
        System.out.println(str1 == str3);//false
    }
}
```



JVM在工作的过程中，会专门创建一片内存空间来存入String对象，这片空间称之为字符串常量池。



1.`String str1 = "qsl";`JVM首先会在字符串常量池内看能否找到字符串`qsl`，如果能找到，直接返回给`str1`，否则就会创建新的String对象放到字符串常量池中，所以这里`str1`和`str2`引用的是同一个对象，所以`str1==str2`会返回true。（`==`对于非基本类型，比较的是两个引用是否引用内存的同一个对象，引用地址的比较）

2.`String str3 = new String("qsl");`JVM首先在字符串常量池中寻找字符串`qsl`，如果找到不做任何事情，如果找不到就创建一个新的对象放到字符串常量池中。由于遇到了new，还会**在内存上（不是字符串常量池中）**创建String对象存储`qsl`，并将**内存上（不是字符串常量池中）**的String对象返回给`str3`。所以`str1==str3`返回false，不是引用同一个对象。



**常量池中的“对象”是在编译期就确定好了的，在类被加载的时候创建的，如果类加载时，该字符串常量在常量池中已经有了，那这一步就省略了。堆中的对象是在运行期才确定的，在代码执行到new的时候创建的。**



## 思考String设计成不可变的原因

1. 字符串常量池的需要。字符串常量池的诞生就是为了**提升效率和减少内存分配**。因为我们有很大部分时间是在处理字符串，其中就会出现大量重复的情况。设计成不可变，常量池容易被管理和优化
2. 安全性考虑。正是因为字符串使用情况多，所以不可变可以有效的防止字符串被有意或者无意的篡改。



# 3.如何理解`String`的`intern`方法?

答：见[《和String有关的那些事儿-intern方法》](/和String有关的那些事儿-intern方法.html)




# 4.String 中 hashcode 是怎么实现的？

源码如下：

```java
public int hashCode() {
    int h = hash;
    if (h == 0 && value.length > 0) {
        char val[] = value;

        for (int i = 0; i < value.length; i++) {
            h = 31 * h + val[i];
        }
        hash = h;
    }
    return h;
}
```



# 5.String、 StringBuilder、 StringBuffer

- String是一个不可变的字符序列
- StringBuffer,StringBuilder是可变的字符序列
  - StringBuffer是jdk1.0版本的,内部使用synchronized，是线程安全的,效率低
  - StringBuilder是jdk1.5版本的,是线程不安全的,效率高



# 4.为什么针对安全保密高的信息，char[]比String更好?

因为String是不可变的，就是说它一旦创建，就不能更改了，直到垃圾收集器将它回收走。而字符数组中的元素是可以更改的（译者注：这就意味着你就可以在使用完之后将其更改，而不会保留原始的数据）。所以使用字符数组的话，安全保密性高的信息(如密码之类的)将不会存在于系统中被他人看到。








