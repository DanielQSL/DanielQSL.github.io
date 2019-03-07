---
title: 探索Java8之Lambda表达式
date: 2019-02-10 10:08:22
categories:
- Java基础
tags:
- Java基础
- Lambda
---



# 前言

JDK8的发布虽然已经过去很长时间了，里面新增的Lambda模块无疑是大家最期待的，从此多了一种新的编程方式：函数式编程。由于工作项目中用的还是1.7版本，所以Lambda表达式使用的不是很多，自己就私下收集资料学习。



# 为什么需要Lambda表达式

我们先来看下Java 8新的语法特性给我们编程带来的便利之处。

在有Lambda表达式之前，新建一个线程，应该这样写：

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("Thread run......");
    }
}).start();
```

有lambda表达式之后，则可以这样写：

```java
new Thread(() -> System.out.println("Thread run......")).start();
```



是不是代码变得更加简洁了，这也是lambda表达式最常见的用法：**简化Java中接口式的匿名内部类**，这种接口被称为**函数式接口**（有且仅有一个抽象方法，但是可以有多个非抽象方法的接口）。

定义一个函数式接口如下：

```java
interface GreetingService 
{
    void sayMessage(String message);
}
```

在调用的时候，可以使用Lambda表达式来表示该接口的实现：

```java
GreetingService greetService1 = message -> System.out.println("Hello " + message);
```



## Lambda表达式的多种写法

**基本语法**：
(parameters) -> expression 或 (parameters) ->{ statements; } 
即: 参数 -> 带返回值的表达式/无返回值的陈述



```java
Runnable run = () -> System.out.println("Hello World");
ActionListener listener = event -> System.out.println("button clicked");//带参数的，只有一个参数类型，括号可以省略
Runnable multiLine = () -> {
    System.out.println("Hello ");
    System.out.println("World");
};
BinaryOperator<Long> add = (Long x, Long y) -> x + y;
BinaryOperator<Long> addImplicit = (x, y) -> x + y;//带两个或两个以上参数，可以省略参数类型，如果只有一行返回值，return也可以省略。
```

以上代码可以得出：

- Lambda表达式是有类型的，赋值操作的左边就是类型。Lambda表达式的类型实际上是**对应接口的类型**。
- Lambda表达式可以包含多行代码，需要用大括号把代码块括起来，就像写函数体那样。
- 大多数时候，Lambda表达式的参数表可以省略类型。这得益于javac的**类型推导**机制，编译器可以根据上下文推导出类型信息。



# Lambda表达式与Stream

Lambda表达式另一个常见的用法，就是和Stream一起使用。Stream就是一组元素的序列，支持对这些元素进行各种操作，而这些操作是通过Lambda表达式指定的。可以把Stream看作Java Collection的一种视图，就像迭代器是容器的一种视图那样（但Stream不会修改容器中的内容）。下面例子展示了Stream的常见用法。



```java
List<Integer> integers = Arrays.asList(4,5,6,1, 2,3,7,8,8,9,10);
 
List<Integer> evens = integers.stream().filter(i -> i % 2 == 0)
        .collect(Collectors.toList()); //过滤出偶数列表 [4,6,8,8,10]
List<Integer> sortIntegers = integers.stream().sorted()
        .limit(5).collect(Collectors.toList());//排序并且提取出前5个元素 [1,2,3,4,5]
 
List<Integer> squareList = integers.stream().map(i -> i * i).collect(Collectors.toList());//转成平方列表
 
int sum = integers.stream().mapToInt(Integer::intValue).sum();//求和
 
Set<Integer> integersSet = integers.stream().collect(Collectors.toSet());//转成其它数据结构比如set
 
Map<Boolean, List<Integer>> listMap = integers.stream().collect(Collectors.groupingBy(i -> i % 2 == 0)); //根据奇偶性分组
 
List<Integer> list = integers.stream().filter(i -> i % 2 == 0).map(i -> i * i).distinct().collect(Collectors.toList());//复合操作
```

从上面的例子中可以看出通过Stream和Lambda配合使用之后，代码变得更简洁了。（后面会专门写一篇关于Stream操作的文章）



# Lambda和Stream的性能

虽然Lambda表达让我们编程变得更简洁，但是它与传统的方式在性能上会有多少区别呢？下面这篇文章是我在网上看到对其相关测试。（如有什么问题，欢迎与我交流）

[《Java8 Lambda表达式和流操作如何让你的代码变慢5倍》](http://www.importnew.com/17262.html)

从测试结果上来看，在数据量很大的情况下，使用传统方式的性能会更好。



# 总结

Java 8引入Lambda表达式，从此打开了函数式编程的大门。虽然性能上表现出来的不是很好，但是对于数据量不大的数据还是可以拿来处理的，我们要与时俱进，这种编程方式以后肯定会流行起来的。