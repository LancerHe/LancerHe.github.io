---
title: CSV格式不支持UTF-8编码，多语言乱码
author: 谇雨
layout: post
permalink: /csv-utf8-muti-language.html
views:
  - 2047
categories:
  - PHP
tags:
  - CSV
  - utf-8
---
由于我们的开发模式一般选择UTF-8编码，所以当存在简体中文，繁体中文，韩日，日文时候，一般页面是不会出现问题的，但是今天做了个项目当导出的CSV的数据存在多国，不同语言的数据时候，数据将会出现乱码。  
而以往的案例一般只做一种语言如：  
mb\_convert\_encoding($content,&#8221;gb2312&#8243;,&#8221;UTF-8&#8243;)，转出来的CSV没任何问题。  
但是如果这时候数据中存在一些日文或者韩文，这些数据将会乱码。

经过资料查找，发现其实是CSV格式并不支持UTF-8的编码。但是他支持UTF-16的编码  
可用此方法解决CSV中的编码问题

<pre class="lang:php decode:true " >//输出BOM
header('Cache-control: private');
header('Content-Disposition: attachment; filename=test.csv');
header('Content-type: text/csv; charset=UTF-16LE');
echo(chr(255).chr(254));
echo(mb_convert_encoding($content,"UTF-16LE","UTF-8"));
</pre>

但是打开Excel还是可能出现一定的问题。

最终的解决方案还是采用开源phpExcel类直接生成Excel。