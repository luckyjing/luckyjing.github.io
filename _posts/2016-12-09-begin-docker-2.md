---
title: "新手学Docker（2）熟悉Run命令"
date: "2016-12-09"
description: "从Docker Hub上 Pull Image，使用Nginx Image熟悉Docker的基本命令"
---

## 上篇回顾

在上篇文章里，我们了解了`Docker`的基本原理和设定，`run`命令可以运行一个镜像，也可以称之为实例化。

## 本篇概览

在本小节里，我们将从`Image`仓库里拉取并运行`Nginx`，尝试下将本地文件和`container`文件相映射，`Nginx`相比`hello-world`多了些启动参数，我们将逐个攻破。

### 准备应用

我们在本机新建一个项目文件夹，在里面创建我们的静态资源文件，随后往`index.html`随便写点内容，执行下`pwd`命令获取一下当前文件夹所在路径。

```
mkdir class01 && cd class01
touch index.html
pwd
```
### 获取Nginx Image

我们打开国内的[Docker Hub镜像站](https://hub.daocloud.io)，搜索`Nginx`，第一个便是我们所需要的，打开Image详情页，会有详细地使用说明，我们先使用命令行方式去运行这个`Image`。

```
docker pull nginx
docker run --name my-nginx  -d -p 3000:80 nginx
```

命令解释：

- `--name`为当前创建的`container`起一个好记的昵称，默认情况下`docker`会随机分配一个昵称
- `-d`即Deamon,能够让进程在后台运行而不阻塞命令行。
- `-p`端口暴露，nginx通常运行在80端口，为了能够在主机访问`container`里的`nginx`，所以需要进行**端口暴露**，格式为：`[host port]:[container port]`

这时我们在本机访问`localhost:3000`，将会看到`nginx`的欢迎页。

![](http://7xlqsb.com1.z0.glb.clouddn.com/%E6%88%AA%E5%9B%BE%202016-12-09%2017%E6%97%B655%E5%88%8618%E7%A7%92.jpg)

**挂载本机目录**

如果想让`nginx`伺服本机的某个目录，我们需要加上`-v`命令，`-v`即Volume，我们希望`nginx`能够以当前文件夹作为资源目录，这个过程称为挂载，通过`[Host dir]:[container dir]`的形式将主机的目录覆盖`container`里面的目录，当然`b`目录本来的内容不会丢失，当我们不选择挂载时，其又将会生效。

```bash
docker ps # 显示当前正在运行的container，我们可以找到刚刚创建的my-nginx
docker stop my-nginx
docker rm my-nginx # 停止并删除my-nginx
docker run --name my-nginx -v [your pwd]:/usr/share/nginx/html:ro -d -p 3000:80 nginx
```

添加的`[your pwd]`为最开始创建的`index.html`所在的文件夹，如`~/coding/docker-learn/class01`

命令解释：

- `:ro`意为**Read Only**，对`container`增加只读权限，默认为可读写

我们再次访问`localhost:3000`，显示的内容已经是我们创建的`index.html`了。

![](http://7xlqsb.com1.z0.glb.clouddn.com/%E6%88%AA%E5%9B%BE%202016-12-09%2017%E6%97%B657%E5%88%8626%E7%A7%92.jpg)

**复用挂载的目录**

上一步我们为`my-nginx`这个`container`挂载了数据卷，如果我们新的`container`也想用和`my-nginx`相同的数据卷呢？我们可以使用`--volumns-from`来复用数据卷地址。

```bash
docker run --name my-nginx1 --volumes-from my-nginx -d -p 3001:80 nginx
```

现在访问`localhost:3001`便可以得到同3000端口一样的效果了，试试`docker ps`看看目前运行中的`container`信息吧。
