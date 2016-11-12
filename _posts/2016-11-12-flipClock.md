---
title:  "有趣的倒计时组件"
date:   2016-11-12
description: 一行代码，生成非常细腻的倒计时画面
---

<iframe src="/flipclock" frameborder="0" style="width:100%;min-height:250px;">

</iframe>

实现以上效果仅仅需要以下几行代码：

```js
var clock = $('.demo-clock').FlipClock(3600 * 24 * 7,{
		clockFace: 'DailyCounter',
		countdown: true
});
```

此组件称为`flipclock`,效果出众并且API十分简单，可以基于它做很多的扩展。

> 官网地址：[http://flipclockjs.com/](http://flipclockjs.com/)