---
title: jQuery实现鼠标点击Div区域外隐藏Div
author: 谇雨
layout: post
permalink: /jquery-click-div-outside-hide-div.html
views:
  - 2723
categories:
  - 前端设计
tags:
  - Div区域外
  - 点击
  - 隐藏Div
---
[<img src="http://www.crackedzone.com/wp-content/uploads/2012/08/divout.jpg" alt="" title="divout" width="590" height="150" class="alignnone size-full wp-image-720" />][1]

jQuery本身没有这样一个事件，要实现这个效果，我们首先要先了解javascript中的事件冒泡。

> 冒泡定义：当一个元素上的事件被触发的时候，比如说鼠标点击了一个按钮，同样的事件将会在那个元素的所有祖先元素中被触发。这一过程被称为事件冒泡；这个事件从原始元素开始一直冒泡到DOM树的最上层。（摘自网络）

<!--more-->

<pre class="lang:xhtml decode:true " >&lt;!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"&gt;
&lt;html xmlns="http://www.w3.org/1999/xhtml"&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=utf-8" /&gt;
&lt;head&gt;
&lt;title&gt;Lancer - Jquery 事件冒泡&lt;/title&gt;
&lt;script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.4.4/jquery.min.js"&gt;&lt;/script&gt;   
&lt;/head&gt;
&lt;body onclick="alert('document')"&gt;
    &lt;div id="outer" onclick="alert('outer');"&gt;
         &lt;div id="inner" onclick="alert('inner')"&gt;
             &lt;a id="link" href="http://www.baidu.com" onclick="alert('link')"&gt;Click&lt;/a&gt;
         &lt;/div&gt;
    &lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>

上述代码运行后，点击链接，以此会出现link, inner, outer, document,转向百度。  
也就是说我们点击链接后 click事件一直冒泡传递在最外层body。

<h4 style="color:#F72">
  1. 阻止事件冒泡, 并不阻止事件行为：event.stopPropagation();
</h4>

<pre class="lang:js decode:true " >$(function() {
	$("#link").click(function(event) {
		event.stopPropagation();
	});
}); </pre>

上述代码运行后，点击链接，按顺序会出现link, 转向百度。

<h4 style="color:#F72">
  2. 阻止事件行为，并阻止事件冒泡 ：event.preventDefault();
</h4>

<pre class="lang:js decode:true " >$(function() {
	$("#link").click(function(event) {
		event.preventDefault(); 
	});
}); 
</pre>

上述代码运行后，点击链接，按顺序会出现link, inner, outer, document, 但是不转向百度。

<h4 style="color:#F72">
  3. 阻止事件冒泡, 也阻止事件行为：return false;
</h4>

<pre class="lang:js decode:true " >$(function() {
	$("#link").click(function(event) {
		return false;
	});
});
</pre>

上述代码运行后，点击链接，只会出现link。

因此我们可以看出事件冒泡不一定是个坏的东西，有些情况会让不同层次捕获不同的事件！

所以鼠标点击Div区域外隐藏Div正需要用到事件冒泡。

<pre class="lang:xhtml decode:true " >&lt;!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"&gt;
&lt;html xmlns="http://www.w3.org/1999/xhtml"&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=utf-8" /&gt;
&lt;title&gt;鼠标点击Div区域外隐藏Div&lt;/title&gt;
&lt;script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.4.4/jquery.min.js"&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;style&gt; 
body { background:black; } 
#demo { background: #fff; width:250px; height:250px; display:none; 　} 
&lt;/style&gt; 
&lt;div id="demo"&gt; 
	&lt;p&gt;This is a div.&lt;/p&gt;
    &lt;p&gt;This is a demo.&lt;/p&gt;
    &lt;p&gt;&lt;input type="text"  /&gt;&lt;br /&gt;
    &lt;select&gt;
    	&lt;option&gt;Test&lt;/option&gt;
    &lt;/select&gt;&lt;/p&gt; 
&lt;/div&gt; 
&lt;input id="btn" type="button" value="显示DIV" /&gt; </pre>

在jquery click事件上设置click事件：  
首先在document上添加一个点击隐藏Div的事件  
然后分别在点击button和div的时候防止冒泡到document

<pre class="lang:default decode:true " >$("#btn").click(function (event) { 
	$("#demo").fadeIn(); 
	$(document).one("click", function () {//对document绑定一个影藏Div方法 
		$("#demo").hide(); 
	});
	event.stopPropagation();//点击Button阻止事件冒泡到document 
}); 
$("#demo").click(function (event) { 
	event.stopPropagation();//在Div区域内的点击事件阻止冒泡到document 
}); </pre>

 [1]: http://www.crackedzone.com/wp-content/uploads/2012/08/divout.jpg