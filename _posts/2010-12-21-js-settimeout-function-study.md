---
title: JS延迟加载(setTimeout)方法注意点
author: 谇雨
layout: post
permalink: /js-settimeout-function-study.html
views:
  - 600
categories:
  - 前端设计
tags:
  - js
  - setTimeout
  - 延迟
---
为了让一个广告延迟几秒加载，或者延迟几秒做页面跳转，发现使用了Javascript的setTimeout实现，还是非常灵活的

延迟载入广告JS

<pre class="lang:xhtml decode:true " ><script language="JavaScript" src="" id="my"></script> 
<script> 
setTimeout("document.getElementById('my').src='js/ad.js'; ",3000); 
</script></pre>

<!--more-->

  
setTimeout的标准语法是: setTimeout(表达式,时间(毫秒)) 两个参数.

但这里要注意的是setTimeout的使用方法，主要在于第一次参数：

1. 函数无参: 

<pre class="lang:js decode:true " >function alertMsg() {
	alert("Error");
}

//可用两种方式

setTimeout(alertMsg,3000);
 
setTimeout("alertMsg()",3000);</pre>

2. 函数有参:

<pre class="lang:js decode:true " >function alertMsg(event){
	alert("keyCode="+event.keyCode);
}

//此时应设置如下方式，不然会提示参数未定义。
setTimeout(function(){alertMsg(event);},3000); </pre>