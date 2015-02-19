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

[<img class="alignnone size-full wp-image-764" title="li-ie7-ie8" src="http://www.crackedzone.com/wp-content/uploads/2012/12/li-ie7-ie8.jpg" alt="" width="500" height="250" />][1]

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
}</pre>

其次是HTML结构

<pre class="lang:xhtml decode:true" title="更简单的HTML定义">&lt;div class="box"&gt;
    &lt;h3&gt;City&lt;/h3&gt; 
    &lt;ul class="city"&gt;        
        &lt;li&gt;厦门&lt;/li&gt;&lt;li&gt;桂林&lt;/li&gt;&lt;li&gt;张家界&lt;/li&gt;&lt;li&gt;贵州&lt;/li&gt;&lt;li&gt;新疆&lt;/li&gt;&lt;li&gt;宁夏&lt;/li&gt;&lt;li&gt;四川&lt;/li&gt;&lt;li&gt;重庆&lt;/li&gt;&lt;li&gt;东北&lt;/li&gt;&lt;li&gt;北京&lt;/li&gt;&lt;li&gt;云南&lt;/li&gt;&lt;li&gt;西藏&lt;/li&gt;&lt;li&gt;华东&lt;/li&gt;&lt;li&gt;甘南&lt;/li&gt;&lt;li&gt;江西&lt;/li&gt;&lt;li&gt;三峡&lt;/li&gt;&lt;li&gt;海南&lt;/li&gt;&lt;li&gt;山东&lt;/li&gt;&lt;li&gt;青海&lt;/li&gt;&lt;li&gt;夕阳红&lt;/li&gt;
    &lt;/ul&gt;  
&lt;/div&gt;</pre>

#### 解决方案

其实方案很简单，就是在Li中添加一条样式 white-space: nowrap;

<pre class="lang:default decode:true" title="Li中加入white-space: nowrap">.city li{
    float:left; 
    list-style: none; 
    margin: 5px; 
    white-space: nowrap;
}</pre>

 [1]: http://www.crackedzone.com/wp-content/uploads/2012/12/li-ie7-ie8.jpg