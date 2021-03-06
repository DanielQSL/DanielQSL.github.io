---
title: 完全二叉树、平衡二叉树、二叉查找树的区别
date: 2018-12-26 22:25:21
categories: 
- 数据结构
tags:
- 数据结构
- 树
---



# 完全二叉树

完全二叉树是一种特殊的二叉树，满足以下要求：

1. 所有叶子节点都出现在 k 或者 k-1 层，而且从 1 到 k-1 层必须达到最大节点数；
2. 第 k 层可以不是满的，但是第 k 层的所有节点必须集中在最左边。 
   需要注意的是不要把完全二叉树和“满二叉树”搞混了，完全二叉树不要求所有树都有左右子树，但它要求：
3. 任何一个节点不能只有右子树没有左子树
4. 叶子节点出现在最后一层或者倒数第二层，不能再往上

用一张图对比下“完全二叉树”和“满二叉树”：

![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jb2uskfsj30b104zgmz.jpg)

当我们用数组实现一个完全二叉树时，叶子节点可以按从上到下、从左到右的顺序依次添加到数组中，然后知道一个节点的位置，就可以轻松地算出它的**父节点**、**孩子节点**的位置。

以上面图中完全二叉树为例，标号为 2 的节点，它在数组中的位置也是 2，它的父节点就是 (k/2 = 1)，它的孩子节点分别是 (2k=4) 和 (2k+1=5)，别的节点也是类似。



## 完全二叉树使用场景：

根据前面的学习，我们了解到完全二叉树的特点是：“叶子节点的位置比较规律”。因此在对数据进行**排序或者查找**时可以用到它，比如**堆排序**就使用了它，后面学到了再详细介绍。



# 二叉查找树

二叉树的提出其实主要就是为了提高查找效率，比如我们常用的 `HashMap` 在处理哈希冲突严重时，拉链过长导致查找效率降低，就引入了红黑树。

我们知道，二分查找可以缩短查找的时间，但是它要求 **查找的数据必须是有序的**。每次查找、操作时都要维护一个有序的数据集，于是有了二叉查找树这个概念。

二叉查找树（又叫二叉排序树），它是具有下列性质的二叉树：

1. 若左子树不空，则左子树上所有结点的值均小于它的根结点的值；
2. 若右子树不空，则右子树上所有结点的值均大于或等于它的根结点的值；
3. 左、右子树也分别为二叉排序树。

如下图所示： 

![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jb34n37dj308a064dg6.jpg)



也就是说，**二叉查找树中，左子树都比节点小，右子树都比节点大，递归定义**。

根据二叉排序树这个特点我们可以知道：**二叉排序树的中序遍历一定是从小到大的**。

比如上图，中序遍历结果是：

> 1 3 4 6 7 8 10 13 14



## 二叉排序树的性能

在最好的情况下，二叉排序树的查找效率比较高，是 O(logn)，其访问性能近似于折半查找；

但最差时候会是 O(n)，比如插入的元素是有序的，生成的二叉排序树就是一个链表，这种情况下，需要遍历全部元素才行（见下图 b）。

![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jb3f95cbj30dz07awfx.jpg)



如果我们可以保证二叉排序树不出现上面提到的极端情况（插入的元素是有序的，导致变成一个链表），就可以保证很高的效率了。

但这在插入有序的元素时不太好控制，按二叉排序树的定义，我们无法判断当前的树是否需要调整。

因此就要用到平衡二叉树（AVL 树）了。



# 平衡二叉树

平衡二叉树的提出就是为了保证树不至于太倾斜，尽量保证两边平衡。因此它的定义如下：

1. 平衡二叉树要么是一棵空树
2. 要么保证左右子树的高度之差不大于 1
3. 子树也必须是一颗平衡二叉树

也就是说，树的两个左子树的高度差别不会太大。

那我们接着看前面的极端情况的二叉排序树，现在用它来构造一棵平衡二叉树。

以 12 为根节点，当添加 24 为它的右子树后，根节点的左右子树高度差为 1，这时还算平衡，这时再添加一个元素 28：

![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jb3pcxvsj308d08kwf1.jpg)

这时根节点 12 觉得不平衡了，我左孩子一个都没有，右边都有俩了，超过了之前说的最大为 1，不行，给我调整！

于是我们就需要调整当前的树结构，让它进行旋转。

因为最后一个节点加到了右子树的右子树，就要想办法给右子树的左子树加点料，因此需要逆时针旋转，将 24 变成根节点，12 右旋成 24 的左子树，就变成了这样（有点丑哈哈）：

![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jb3zzj3gj307z04ut90.jpg)

这时又恢复了平衡，再添加 37 到 28 的右子树，还算平衡：

![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jb49abqpj30ae07e3zc.jpg)

这时如果再添加一个 30，它就需要在 37 的左子树：

![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jb4j733kj309q0abmyc.jpg)

这时我们可以看到这个树又不平衡了，以 24 为根节点的树，明显右边太重，左边太稀，想要保持平衡就 24 得让位给 28，然后变成这样：

![](http://ww1.sinaimg.cn/large/007P9bxgly1g3jb4y3w5nj30a107wjsf.jpg)

丑了点，但的确保持了平衡。

依次类推，平衡二叉树在添加和删除时需要进行旋转保持整个树的平衡，内部做了这么复杂的工作后，我们在使用它时，插入、查找的时间复杂度都是 O(logn)，性能已经相当好了。



# 总结

阅读完这篇文章，相信你对“完全二叉树、二叉查找树和平衡二叉树”这三个二叉树有了更加深刻的认识，有了这个基础，再去看 B+ B- 红黑树，就相对容易些了。