---
title: css解决图片垂直居中
author: 谇雨
layout: post
permalink: /css-solve-images-vertical-align.html
views:
  - 114
categories:
  - 前端设计
tags:
  - CSS
  - 图片
  - 垂直居中
---
本文转载至：<a href="http://playgoogle.com/?p=85" target="_blank">http://playgoogle.com/?p=85</a>

使用纯CSS实现未知尺寸的图片(但高宽都小于200px)在200px的正方形容器中水平和垂直居中。

```
.box {
        /*非IE的主流浏览器识别的垂直居中的方法*/
        display: table-cell;
        vertical-align:middle;
        /*设置水平居中*/
        text-align:center;
        /* 针对IE的Hack */
        *display: block;
        *font-size: 175px;/*约为高度的0.873，200*0.873 约为175*/
        *font-family:Arial;/*防止非utf-8引起的hack失效问题，如gbk编码*/
        width:200px;
        height:200px;
        border: 1px solid #eee;
}
.box img {
        /*设置图片垂直居中*/
        vertical-align:middle;
}

<div class="box">
        <img src="http://www.playgoogle.com/image/logo/playgoogleLogo.jpg" />
</div>
```

这种解决方法的好处是不用加一些不必要的xhtml代码。缺陷是需要已知容器的高度，在容器高度未知时，无法处理。