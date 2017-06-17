---
title: HttpClient模拟POST获取返回值
author: 谇雨
layout: post
permalink: /httpclient-post-request-return.html
views:
  - 1102
categories:
  - PHP
tags:
  - HttpClient
  - POST
  - 返回值
---
<p style="text-indent: 2em;">
  在没有表单的情况下PHP很难实现POST数据，如果采用URL方式GET传值，那么比较大的数据是无法传过去。而HttpClinet这个类去很好，跨域的情况下也可以直接模拟POST到其他页面中，同时可以获取返回值。
</p>

<!--more-->

<p style="text-indent: 2em;">
  下载地址：<a href="http://scripts.incutio.com/httpclient/" target="_blank">http://scripts.incutio.com/httpclient/</a>
</p>

<coolcode lang="php" linenum="off">$postUrl = &#8216;http://www.example.com/checkemail.php';  
$postArr = array(  
&#8216;email&#8217; => $email,  
&#8216;id&#8217; => $id,  
&#8216;key&#8217; => $key  
);  
//把页面执行后的HTML写入$getReturn  
$getReturn = HttpClient::quickPost($postUrl, $postArr);  
echo $getReturn;</coolcode>