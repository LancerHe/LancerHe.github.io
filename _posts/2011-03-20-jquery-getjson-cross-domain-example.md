---
title: jquery中getJSON跨域示例
author: 谇雨
layout: post
permalink: /jquery-getjson-cross-domain-example.html
views:
  - 492
categories:
  - PHP
  - 前端设计
tags:
  - getJSON
  - jquery
  - 跨域
---
> 在 jQuery 1.2 中，您可以通过使用JSONP 形式的回调函数来加载其他网域的JSON数据，如 &#8220;myurl?callback=?&#8221;。jQuery 将自动替换 ? 为正确的函数名，以执行回调函数。 注意：此行以后的代码将在这个回调函数执行前执行。 

<pre class="lang:js decode:true " >$.getJSON('http://192.168.0.144/test/testjson.php?callback=?',function (json){
        alert(json[1].name);</pre>

php代码：

<pre class="lang:php decode:true " >$arr[] = array('id'=>1,'name'=>'PHP');
$arr[] = array('id'=>2,'name'=>'JSP');

$json_str = json_encode($arr);

echo $_GET['callback'].'('.$json_str.')';
?></pre>

jquery中利用getJSON这个方法就可以轻松搞定跨域传值问题了。