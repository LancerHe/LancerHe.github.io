---
title: 在FF和IE下实现图片垂直居中
author: 谇雨
layout: post
permalink: /ff-ie-image-vertical-center.html
views:
  - 68
categories:
  - 前端设计
tags:
  - CSS
  - 图片
  - 垂直居中
---
相信大家都知道CSS中vertical-align:middle;完全就是个可有可无的东西。。因此实现一堆缩略图的垂直居中显得非常之麻烦。

一般来说在div中实现文字垂直居中可以做到：比如div {height:200px;line-height:200px;}只要把height和line-height设置差不多就好办了。但是假如是图片。。那么在FF中必然over了。。  
无意间。我发现。。只要在图片前面有文字或者空格。就居中。。无奈中。。

<pre class="lang:xhtml decode:true " >&lt;style type="text/css"&gt;
#idiv {height:200px;width:200px;border:1px solid black;line-height:200px;}
&lt;style&gt;
&lt;body&gt;
&lt;div id="idiv"&gt;
&nbsp;
&lt;img src="http://bbs.blueidea.com/images/smilies/default/eek.gif" /&gt;
&lt;/div&gt;</pre>