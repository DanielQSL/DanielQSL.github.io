---
title: 【转】不止JDK7的HashMap，JDK8的ConcurrentHashMap也会造成CPU 100%
date: 2019-01-11 21:53:10
categories: 
- Java并发
tags:
- Java并发
- HashMap
- ConcurrentHashMap
---



> 转载自：https://blog.csdn.net/u013256816/article/details/84917112



# 现象

大家可能都听过JDK7中的HashMap在多线程环境下可能造成CPU 100%的现象，这个由于在扩容的时候put时产生了死链，由此会在get时造成了CPU 100%。这个问题在JDK8中的HashMap获得了解决。其实JDK7中的HashMap在多线程环境下不止只有CPU 100%这一共怪异现象，它还可能造成插入的数据丢失，有兴趣的读者可以自行了解下。

对于HashMap多线程的问题，我们通常会这么反问：HashMap设计上就不是多线程安全的，何必要去在多线程环境下用呢？的确如此，我们不会傻到显式的在多线程环境下调用，但是又可能在你所关注的视角范围外是多线程的，其隐式地让HashMap置于多线程环境下了，这个又难以一下子察觉到。再者，对于HashMap多线程的问题，我们很多时候推荐使用ConcurrentHashMap来代替HashMap应用于多线程的环境，很不巧的是ConcurrentHashMap也有可能会造成CPU 100%的异常现象。这个怪异现象存在于JDK8的ConcurrentHashMap中，在JDK9中已经得到修复，可以参见：https://bugs.openjdk.java.net/browse/JDK-8062841

什么情况下JDK8的ConcurrentHashMap会出现这个Bug呢？首先我们来运行一下这段代码：

```java
map.computeIfAbsent("AaAa",
        (String key) -> {
            map.put("BBBB", "value");
            return "value";
        });
```

你会惊奇的发现这个程序一直处于Running状态，我们通过top -Hp [pid]命令查看到其中一个线程的CPU使用率接近100%，参考下图：

![Imgur](https://i.imgur.com/qxqLDfd.png)

可以看到pid为31417的东东，我们再通过jstack -l [pid]命令查看到对应的线程为：

![Imgur](https://i.imgur.com/GaiCXjX.png)

注意将nid=0x7ab9的16进制转为10进制就是31417。可以看到问题是发生在了computeIfAbsent方法中，我们将示例中的程序换成下面这段程序也会同样出现CPU 100%的Bug：

```java
static Map<Integer, Integer> concurrentMap = new ConcurrentHashMap<>();

public static void main(String[] args) {
    System.out.println("Fibonacci result for 20 is" + fibonacci(20));
}

static int fibonacci(int i) {
    if (i == 0)
        return i;

    if (i == 1)
        return 1;

    return concurrentMap.computeIfAbsent(i, (key) -> {
        System.out.println("Value is " + key);
        return fibonacci(i - 2) + fibonacci(i - 1);
    });
}
```

至于为什么会发生这个BUG，答案就在ConcurrentHashMap中的computeIfAbsent方法中，自己去捞吧，嘿嘿。

# 原因

```java
map.computeIfAbsent(key1, mappingFunction)
```

如果当前key1-hash对应的tab位(可以理解为槽)刚好是空的,在计算mappingFunction之前会

- step1: 先往对应位置放一个ReservationNode占位
- step2: 然后计算mappingFunction的值value,
- step3: 再将value组装成最终NODE, 把占位的ReservationNode换成最终NODE;

这时如果:
mappingFunction 中用到了 当前map的computeIfAbsent方法, 很不巧 key2-hash的槽为和key1的是同一个,
因为key1已经在槽中放入了占位节点, 在处理key2时候for循环的所以处理条件都不符合 程序进入了死循环

但是如果:

key2-hash的槽位和key1的不一样, 是不会发生死循环

多线程问题:

因为ConcurrentHashMap在处理上述step1-step3是同步的, 而且在处理时候会同步获取的值, 所以是不存在线程不安全的, 纯粹是当前线程死循环

Thread1 通过cas 在槽x放了个ReservationNode(RN1), 然后假设mappingFunction执行的很慢

Thread2 在槽x和Thread竞争, cas失败没有抢到占位符; 进行下一轮for循环, 这是因为槽x中已经被放置了RN1, 所以Thread2获取到这个RN1,在执行synchronized(RN1) 时候被thread1block住

code sample:

```java
public static void main(String[] args) throws IOException {
    ConcurrentHashMap<String, String> map = new ConcurrentHashMap<>();
    //caseA: dead loop
//        map.computeIfAbsent("AA", key -> map.computeIfAbsent("BB", k->"bb"));

    //caseB: block, but no dead loop
    new Thread(()->map.computeIfAbsent("AA", key -> waitAndGet())).start();

    new Thread(()->{
        try {
            TimeUnit.SECONDS.sleep(3);  //delay 1 second
        } catch (InterruptedException e) {}
        map.computeIfAbsent("BB", key-> "bb");
    }).start();

}

private static String waitAndGet(){
   try {
       TimeUnit.SECONDS.sleep(20);
   } catch (InterruptedException e) {
   }
   return "AAA";
}
```



# 解决

怎么规避这个问题呢？只要不在递归中使用computeIfAbsent方法就好啦，或者降级用可爱的分段锁，或者升级JDK9~