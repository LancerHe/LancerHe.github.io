---
title: PHP中去除特殊空白换行方法
author: 谇雨
layout: post
permalink: /php-strip-blank.html
views:
  - 88
categories:
  - PHP
tags:
  - blank
  - strip
---
**第一种写法: **指定替换，根据需要替换掉。

$str = str_replace(array("\r\n", "\r", "\n"), "", $str);

**第二种写法:** PHP_EOL是php规定的特殊的转义字符，比较推荐使用换行缩进的时候使用

$str = str_replace(PHP_EOL, '', $str); 

**第三种写法:** 正则替换所有[\s]特殊字符，这种方法比较狠，有时候第二种无法达到效果的时候，可以考虑用这个。

$str = preg_replace("/\s/","",$str);