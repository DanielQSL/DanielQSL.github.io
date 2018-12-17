---
title: Hexo-GitHub搭建个人博客网站教程
date: 2018-12-17 13:40:22
categories: 
- 搭建博客
- 初学
tags: 
- Hexo
- 博客
---

## 一、准备工作

1. Nodejs、npm环境

2. Git环境




## 二、GitHub上建立一个仓库

1. 在GitHub上新建一个仓库

​	仓库的名字的格式必须为：用户名.github.io（如下图）
![Imgur](https://i.imgur.com/U84W6zN.png)

2. 完成以上步骤,我们就拥有了自己的代码仓库

## 三、设置本地博客配置

1. 安装hexo客户端
    在你的计算机中新建一个文件夹,使用git bash进入该文件夹(也可以使用系统自动的cmd),使用npm命令安装Hexo

    ```bash
    npm install -g hexo-cli
    ```


2. 初始化我们的博客,使用一下命令

   ```bash
   hexo init
   ```

3. 等待初始化完成以后,测试是都搭建成功

   ```bash
   hexo s
   ```
4. 这时我们就可以在浏览器的地址栏中输入http://localhost:4000/,我们会看到如下图

![Imgur](https://i.imgur.com/IsGB0bK.png)



## 四、上传代码至GitHub

1. 首先打开项目配置文档_config.yml,对其进行如下图所示修改,此步骤目的是指定项目发布的仓库
![Imgur](https://i.imgur.com/iWX6lda.png)

​      **注:**type,repository等属性冒号后面都必须有一个空格,再填入属性值,否则不会生效.

2. 配置文件修改完成之后,就可以上传代码,切换到命令行窗口,一次输入以下命令

   ```
   npm install hexo-deployer-git --save
   hexo g
   hexo d
   ```

   完成之后,我们就可以上GitHub看到刚刚提交的代码.

