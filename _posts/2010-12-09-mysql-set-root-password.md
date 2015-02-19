---
title: Mysql设置root密码
author: 谇雨
layout: post
permalink: /mysql-set-root-password.html
views:
  - 83
categories:
  - MySql
tags:
  - MySql
  - root
  - 密码
---
常常由于集成环境安装后MySql的密码为空，或者偶尔想改下MySql密码  
倘若我们要设置密码为root123，进入MySql控制台

<pre class="lang:sh decode:true " >mysql&gt; grant all privileges on *.* to 'root'@'localhost' identified by 'root123' with grant option;
mysql&gt; flush privileges;
mysql&gt; exit;</pre>

Ok..登陆MySql就用新密码吧。