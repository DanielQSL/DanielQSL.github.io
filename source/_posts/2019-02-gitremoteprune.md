---
title: git remote prune 删除在本地有但在远程库中已经不存在的分支
date: 2019-02-08 20:47:41
categories:
- Git
tags:
- Git
---



# 背景

在你经常使用的命令当中有一个git branch –r 用来查看远程的分支，包括本地和远程的。但是时间长了你会发现有些分支在远程其实早就被删除了，但是在你本地依然可以看见这些**被删除**的分支。

![Imgur](https://i.imgur.com/6i8KoZ0.png)

图中蓝色圈出来的就是本地显示，但是在远程服务器中不存在的分支。



# 解决方案

通过命令git remote show origin 来查看有关于origin的一些信息。

![Imgur](https://i.imgur.com/G0B5JrW.png)

可以看出这三个不存在的分支，git给出了一个建议，你可以通过`git remote prune origin  `移除这些不存在的分支。（也就是说你可以刷新本地仓库与远程仓库的保持这些改动的同步）



此时再通过git branch –r来查看，这个在远程删除的分支在你本地仓库也将被删除。

![Imgur](https://i.imgur.com/ORh978B.png)



