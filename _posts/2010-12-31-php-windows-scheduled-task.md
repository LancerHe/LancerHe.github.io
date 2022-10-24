---
title: PHP在WINDOWS中执行计划任务
author: 谇雨
layout: post
permalink: /php-windows-scheduled-task.html
views:
  - 400
categories:
  - PHP
tags:
  - php
  - windows
  - 自动发信
  - 计划任务
---
在Linux上通常有定时发送Email或者定时备份数据库的功能，通常是通过设置cron实现。而在Windows也能通过计划任务实现。

在控制面板中开启计划任务(前提是计划任务这服务必须先开启)

写一个PHP页面，如emailcron.php：

//随便写，写自己要的效果，和计划任务无关
require("mail.php");
mail_send('lancer_he@hotmail.com', 'body test', 'test123AAABBBccc');
mail_send('sd6192709@126.com', 'body test', 'test123AAABBBccc');

新建计划任务，在程序处选择php.exe所在位置

参数里写 -f D:\www\emailcron.php

实际的结果就为：D:\php\php.exe -f D:\www\emailcron.php

设置启动方式，和计划周期，比如开机启动和每隔分钟执行一次。

运行计划任务。。你会发现每隔五分钟你的邮箱就收到一份信。