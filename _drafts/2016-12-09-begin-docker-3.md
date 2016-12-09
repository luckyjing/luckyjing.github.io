---
title: "新手学Docker（3）开始使用Dockerfile构建镜像"
date: "2016-12-09"
---

## 上篇回顾

在上篇文章里，我们了解了`Docker`的基本原理和设定，`run`命令可以运行一个镜像，也可以称作实例化。

## 本篇概览

在本小节里，我们将构建一个简单的镜像（Image），以下称为`Image`。

### 准备应用

我们在本机新建一个项目文件夹，在里面创建我们的静态资源文件，额外地需要再创建一个名为`Dockerfile`的文件，它便是我们将本地应用`Docker`化的入口，随后往`index.html`随便写点内容。

```
mkdir class01 && cd class01
touch index.html
touch Dockerfile
```

### 构建Image

打开`Dockerfile`，加入以下内容，`Docker`将会根据下面的指令来一步步构建好`Image`

```
FROM nginx

MAINTAINER LuckyJing "649008270@qq.com"

COPY . /usr/share/nginx/html

```

执行**构建命令**，`Docker`将会根据当前这一瞬间，打一个快照（SnapShot），然后根据`Dockerfile`生成一个`Image`。

```
docker build -t first-app ./
```

