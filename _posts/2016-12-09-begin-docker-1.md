---
title: "新手学Docker（1）Hello World"
date: "2016-12-9"
description: "本篇将讲述Docker的环境搭建，用Hello World实例初步了解Docker"
---

## Docker从哪儿下载？

由于我们访问`Docker`官网一般情况下会非常慢，所以我们不得不把目光投向国内的镜像站，还好，`DaoCloud`为我们扫清了一系列障碍，[点击前往](https://www.daocloud.io)。

你只需要以下步骤：

- 注册一个`DaoCloud`账号
- 进入下载页面，根据自己的系统下载合适的镜像包
- 安装完成后配置[Docker加速器](https://www.daocloud.io/mirror.html#accelerator-doc)
- 打开命令行，执行`docker version`，观察`Docker`是否安装成功

## 入门锦囊

![](http://7xlqsb.com1.z0.glb.clouddn.com/Docker.jpg)

### 名词解释

- Image
    - `Image`是一堆文件的集合体，从用户角度来看，它是一个镜像包，它的特征是只读的，它是别人（或者我们自己）构建好的一个环境，在`Docker`世界里，我们会经常寻找第三方包，来支持我们的工作，如`nginx`,`centos`，我们不修改它，我们基于它实例出的`container`进行工作。
- container
    - `Image`是只读的，我们是不可能也没必要去修改`Image`的，我们要做的就是实例化`Image`，即称为`container`，我们所做的修改都是基于`container`的，如图所示，`container`其实就是`Image`的一份拷贝，但是多了一部分**可读写**的区域，我们就是对这部分进行加工处理的，`container`其实也是一系列文件，真正支持我们交互的是`Running container`，它多出了一个进程，此进程提供我们与`container`读写区域沟通的途径，我们才得以有机会**运行Docker**。

`container`经过我们的处理就像在`git`仓库里修改了文件一样，将修改后的`container`打快照固化为`Image`，这便是`Docker`生态的流动源泉。起初，我们可能只是一个壳子，人们陆陆续续为其增添功能，并且固化，经过一系列的串行作业后，这个壳子镜像凝结了前人的智慧，傲娇地展示在我们面前。

### 命令解释

- `docker run [Image Name]`
    - 检查本地是否有**Image Name**
    - 如果没有的话，则从远端仓库下载
    - 加载`Image`并且实例化`container`（实际上执行了 `docker create` && `docker start`）
- `docker ps`
    - 默认显示当前**正在运行**的`container`
    - 加上`-a`可以显示**创建过**的所有的`container`


## Hello World 1

1. 输入`docker run hello-world`，命令行会打印以下信息

```bash
$ docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
535020c3e8ad: Pull complete
af340544ed62: Pull complete
Digest: sha256:a68868bfe696c00866942e8f5ca39e3e31b79c1e50feaee4ce5e28df2f051d5c
Status: Downloaded newer image for hello-world:latest

Hello from Docker.
```

1.  执行`docker ps -a`，将会显示一个列表，显示错乱往往是因为你的命令行工具太窄了，你可以试着全屏显示。我们启动过的所有的`container`将会在这里显示。
    > 为什么不能直接执行`docker ps`？hello-world这个Image在创建好`container`以后，打印信息，然后自动`stop`这个`container`，所以它已经不在正在运行的`container`列表里了。


## Hello World 2

1. 输入`docker run centos /bin/echo 'hello world'`，这一步我们不仅仅启动了一个`container`，还传输了一个命令进去执行了。
```bash
    docker run centos /bin/echo 'Hello world'

    Hello world
```
1. 输入`docker ps -a`可以看到第一步启动的那个`container`
1. 输入`docker run -t -i centos`可以启动一个交互性的`centos`，如同你正在远程`ssh`连接服务器。

## 扩展阅读
- [10张图带你深入了解Docker容器和镜像](http://dockone.io/article/783)
    - 了解原理后，图解`Docker`的运行命令，非常值得一看！！！
- [前端开发者的Docker之旅](http://docs.daocloud.io/docker-frontend/docker-frontend-open)
    - 作为一名正经前端，当然也要对`Docker`尝尝鲜，在`DaoCloud`官网上发现了一篇非常不错的文章，大家可以据此了解`Docker`。