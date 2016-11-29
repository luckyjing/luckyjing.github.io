---
title:  "学生团队的前后端分离及持续交付探索历程"
date:  2016-11-19
description:  "在学生团队里，大家技术栈参差不齐，开发时间难以统一协调，人员的开发协调，节奏安排已经成为了比具体编写代码更重要的事情，这一次承担起本团队的系统架构设计，旨在提高大家的开发效率，开发专注度，高质量地完成项目。"
---

> 大学的最后一门必修课也已经展开帷幕了，伴随而来的是一次大型的项目大作业，回顾大学三年，在学院课程大作业范围内的项目，并没有一次印象深刻的团队开发经历，往往都是三两程序员一聚，便草草开始了。年已至末，最后一次大作业，需要拥有规范的项目开发流程，能够平稳地推进项目的开发，不会让项目边界越来越大以至于收不住边界，导致最后往往以通宵加班而收场。

## 项目背景及成员情况

### 项目背景

本次的项目是一个在线购物商城系统，这个词相信大家已经耳熟能详了，具体细节不必多说，分为三个子系统`Customer,Owner,Administrator`，需要将项目分为三个阶段，`Release1,2,3`，采用原型开发模式，每个版本给出一个可供交付的产品，总结来说，项目性质为无复杂功能点，但业务量繁多。

### 成员介绍

团队成员共有20人，其中开发同学共10位，前端同学4位，后端同学6位。首先进行技术调研，和在公司不同，在公司大家技术栈其实差不多，在学校则不是，最终得出结果，前端熟悉`React`同学1位，其余3位熟悉`jQuery`，后端同学全部熟悉`SSH框架`，为保证后端的统一，后端技术栈不作调整，但前端因考虑到自动化构建的工具的接入，以及用`jQuery`前期成本小，后期成本大，难以维护的现状，故全部采用`React`技术栈，并对不熟悉的同学进行一次集体培训以及后续答疑解惑。

### 前期准备

三个子系统之间其实前端关联度不大，可以认为是三个独立的系统，对于后端而言，不同数据之间是共享的，故由一名主力后端同学负责数据库的设计与构建，并且搭建好基础后端目录，其他同学在此基础上进行业务层面的开发。

## 项目构建历程

![](http://7xlqsb.com1.z0.glb.clouddn.com/arch.png)

### 基础结构预览

在看过上面这个图以后，项目的整个轮廓已经清晰，为了实现以上架构，我们需要定一些小小目标：
1. 前后端采用分离模式开发，这样大家可以独立进行开发，数据全部通过以`json`为基础格式的接口交流。
2. 后端接口还没有写好的时候，怎么才不会影响前端同学开发，最直接能想到的，打造一个`mock`服务器，这样前端就可以请求接口了，只不过数据都是自己模拟的。
3. 我们只有一台服务器，是很实惠阿里云学生机，所以前后端数据都要放在这台机器上，前端静态资源存储在`nginx`服务器下，后端服务放在`tomcat/webapps`目录下，因为为单页应用，后端只需要在那份`index.html`里引入前端静态资源就可以了（直接写入绝对路径），最终访问的时候，服务器能够辨别是请求静态资源还是后端服务器，也就是俗称的`动静分离`。
4. 本地开发如何部署到服务器上，部署的时候如何轻松，快捷，避免配置各种环境，要配也是一个人配，不能让开发同学苦恼。

下面我们来一步步实现。

### 前端基础架构

1. 前后端分离开发，所以路由要放到前端，用`React-Router`。
2. 前端要自动化构建，用`Webpack`。
3. `React+Redux`配置其实挺麻烦的，如果能写好模板，然后自动生成就好了，用`redux-cli`。

前端使用`React+Redux+React-Router`作为基础框架,使用`Webpack`作为构建工具，使用`mock2easy`作为前端mock服务,在项目开发前，我提前将项目的脚手架提前搭建好，项目的大概目录如下所示：

```
├── README.md
├── blueprints  # 这就是上述第三点的模板，大家可以去github上搜 redux-cli
├── build # 构建后的文件
├── dev.js # 类似webpack-dev-server 加入了mock的服务
├── package.json
├── src
└── webpack.config.js
```

将相关命令写到`package.json`中，前端同学只需执行个命令就可以启动服务了。

```json
  "scripts": {
    "dev": "node dev.js",
    "pub": "NODE_ENV=production webpack && git add . && git commit -m':grin:发布新功能' && git push origin master"
}
```

使用`Webpack`提前写好配置文件，不管`webpack.config.js`里有多少步骤，最终呈现出来的只有`build`目录下那几个打包好的文件，有一点要注意的是，在发布时，最好将`React`,`Redux`这些库抽离出来，这样打包出来文件会小，库文件我们可以用免费的`CDN`进行引入，比如：

```
<script src="//cdn.bootcss.com/react/15.4.0/react.min.js"></script>
<script src="//cdn.bootcss.com/react/15.4.0/react-dom.min.js"></script>
<script src="//cdn.bootcss.com/redux/3.6.0/redux.min.js"></script>
```

目前为止，我们实现了前端项目的自动打包，还缺开发环境，下面我们继续打造。

在开发模式下，`React`,`Redux`就不用被抽离出来了，当然代码也不用压缩了，一切以快为主。

mock服务器什么原理，一张图给大家展示一下。

![](http://7xlqsb.com1.z0.glb.clouddn.com/mock.png)

解释一下，`webpack-dev-server`简单点就是一个`express`的服务器，那么如果我们直接用它的话，直接访问`/ajax/test.json`肯定是跑不起来的，因为这个接口404啊，所以我们要想实现如下目标：既能拥有`webpack-dev-server`独特的热刷新功能，还可以当请求后缀为`xxx.json`的时候，去请求一个`mock`服务器去拿数据然后返回，我们这个`mock`服务使用的是`mock2easy`，大家可以去`GitHub`上搜搜看，一切皆透明，于是我们的`dev.js`配置如下所示：

```javascript
// 先实现一个webpack-dev-server
var app = connect();
var compiler = webpack(config);
app.use(require("webpack-dev-middleware")(compiler, config.devServer));
app.use(require("webpack-hot-middleware")(compiler));
// 核心代码 之 mock服务就是一个中间件
app.use(function(req,res,next){
    if (req.url.indexOf('.json') == -1) {
      return next();
    }
    // 发现是接口请求，然后去请求mock服务器，然后把数据返回
    var _req = http.request(options, function (_res) {
      var data = '';
      _res.on('data', function (chunk) {
        data += chunk;
      });
      _res.on('end', function () {
        res.setHeader('Content-Type', 'application/json; charset=utf-8');
        res.end(data);
      });
    });
});
app.listen(config.devServer.port, function () {
  console.log('开发环境已经启动: http://127.0.0.1:' + port);
});
// 启动mock服务器
require('mock2easynew')(options, function (app) {
  app.listen(options.port, function () {
    console.log('mock服务器已经启动，地址：http://localhost:' + options.port);
  });
});
```

经过以上步骤之后，我们的前端开发环境已经搭建好了，开发同学只要先去填写一些`mock`的API，然后就可以在前端直接访问到了，所以剩下的只需要和后端进行约定下接口名称就可以了。

### 后端简介

1. 后端基本上尽可能也要自动化，快捷化，所以使用`maven`去进行项目的构建，达到出`src+pom.xml`即可四处运行的目的。
2. 使用`Postman`让后端同学进行接口的测试。

## 服务器部署实战

到目前为止，前后端同学都可以在各自本地进行业务开发了，而且相互不耦合，以一份API文档为准便可以开发，接下来要讨论的就是构建的事情了，我们要达到的目的是：**前端打包好后的静态资源可以自动部署到服务器上，后端代码推到仓库后，会自动进行maven构建，打war包，并自动上传到服务器上，进行tomcat服务器的重启**。

为了实现以上目的，项目采用阿里云`code`进行代码管理，与此同时，使用阿里云`CRP`服务实现上述持续化，描述如下：

1. CRP服务检测指定的远程仓库`master`分支是否有更新
2. 如果有更新，则执行一段脚本（由用户输入，往往为一些构建命令）
3. 用户指定需要上传的文件（往往是第二步骤的产出）
4. 用户指定需要上传到的服务器以及路径
4. 用户指定上传结束后需要执行的一段脚本（往往为一些部署前的命令，比如移动静态资源到指定目录，或关闭tomcat，解压缩包，重启tomcat）

还需要对`nginx`做一下简单配置，实现动静分离。

```
# 由nginx处理静态页面
location ~ .*\.(gif|jpg|png|bmp|swf)$
{
  expires 30d;   #使用expires缓存模块，缓存到客户端30天
}
location ~ .*\.(html|jsp|js|css)?$
{
  expires 1d;
}
# 其余请求全部转到tomcat
location ~ .*$ {
  proxy_pass http://127.0.0.1:8080;
}
```

我们集成后的效果如下，当集成完毕后，可以通知测试同学进行及时测试，提前暴露出问题，保证项目的稳步前进。

![](http://7xlqsb.com1.z0.glb.clouddn.com/log.jpg)

## 总结

全程构建系统的过程中，一直遵循的守则是自动化越高越好（`redux-cli`,`maven`,`持续交付`），细节屏蔽的越高越好（执行命令写到`package.json`里），将部署步骤和开发步骤相互隔离，开发同学只需要专注业务的开发即可，这样便可以三个子系统同时前进，开发同学可以有更多的时间去考虑代码本身的质量。

能够让我们放心去构建整个系统的基础，屏蔽底层的不同，多亏了在背后运行的`Docker`服务，本次项目中的尝鲜体验已经感受到了好处，将`Docker`纳入项目的部署环境，往往会带来惊喜。

路还长，保持新鲜感，不断去构造更加完善的自动化系统。