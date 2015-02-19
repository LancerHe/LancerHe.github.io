---
title: apache开启.htaccess支持
author: 谇雨
layout: post
permalink: /apache-open-htaccess-support.html
views:
  - 97
categories:
  - Apache
tags:
  - .htaccess
  - Apache
---
打开apache的配置文件httpd.conf  
1.把 #LoadModule rewrite\_module modules/mod\_rewrite.so 前面的#去掉。  
2.搜索 AllowOverride None 改为 AllowOverride All  
3.重启 Apache 服务器  
<!--more-->