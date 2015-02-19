---
title: 无法加载 mcrypt 扩展解决方案
author: 谇雨
layout: post
permalink: /mcrypt-unload-solutions.html
views:
  - 48
categories:
  - MySql
tags:
  - mcrypt
  - phpmyadmin
---
<p style="text-indent: 2em;">
  关于 phpmyadmin 无法加载 mcrypt 扩展 的问题，一般经过以下4步，都能解决
</p>

<p style="text-indent: 2em;">
  1).在php.ini配置文件中，搜索 ;extension=php_mcrypt.dll<br /> 将php_mcrypt.dll前的 &#8221; ; &#8220;分号去掉改成extension=php_mcrypt.dll
</p>

<p style="text-indent: 2em;">
  2).复制 PHP5\libmcrypt.dll 到 PHP5\ext，用于完整扩展文件。
</p>

<p style="text-indent: 2em;">
  3).从php5中复制 libmcrypt.dll 到system32目录下。
</p>

<p style="text-indent: 2em;">
  4).重启Apache或者IIS。
</p>

<!--more-->