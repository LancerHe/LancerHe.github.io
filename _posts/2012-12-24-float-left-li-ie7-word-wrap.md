---
title: float:left li中文标签在IE7中被自动折行
author: 谇雨
layout: post
permalink: /float-left-li-ie7-word-wrap.html
views:
  - 1062
categories:
  - PHP
tags:
  - float
  - li
  - 中文
  - 折行
---
Li标签Float:left在做网页侧栏的一些tags的时候，是一种很好的选择。  
不过在中文字符换行问题上，可能会出现一点小问题，先看一张IE7和IE8下的效果图吧：

[<img class="alignnone size-full wp-image-764" title="li-ie7-ie8" src="{{ site.url }}/uploads/2012/12/li-ie7-ie8.jpg" alt="" width="500" height="250" />][1]

上图IE7的效果看起来的确很蛋疼，但是在标准浏览器中是不会出现上图IE7的效果的。

首先看下CSS代码结构<!--more-->

<pre class="lang:css decode:true" title="非常简单的Css定义">.city {
    width:200px;
    border: 1px solid black;
    overflow: hidden; 
    padding: 0;
    margin:0;
}
.city li{
    float:left; 
    list-style: none; 
    margin: 5px;
}

其次是HTML结构

<pre class="lang:xhtml decode:true" title="更简单的HTML定义"><div class="box">
    <h3>City</h3> 
    <ul class="city">        
        <li>厦门</li><li>桂林</li><li>张家界</li><li>贵州</li><li>新疆</li><li>宁夏</li><li>四川</li><li>重庆</li><li>东北</li><li>北京</li><li>云南</li><li>西藏</li><li>华东</li><li>甘南</li><li>江西</li><li>三峡</li><li>海南</li><li>山东</li><li>青海</li><li>夕阳红</li>
    </ul>  
</div>

#### 解决方案

其实方案很简单，就是在Li中添加一条样式 white-space: nowrap;

<pre class="lang:default decode:true" title="Li中加入white-space: nowrap">.city li{
    float:left; 
    list-style: none; 
    margin: 5px; 
    white-space: nowrap;
}

 [1]: {{ site.url }}/uploads/2012/12/li-ie7-ie8.jpg