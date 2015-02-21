---
title: 给文章设定置顶截止时间，到时间后自动取消置顶功能
author: 谇雨
layout: post
permalink: /article-top-auto-disappear.html
views:
  - 135
categories:
  - MySql
  - PHP
tags:
  - 文章发布
  - 置顶
  - 自动取消
---
最近搞这个自动取消置顶的东西，非常之烦

一开始方案是采取读出两次数据 ，一组是置顶不过期数据，一组是所有数据。然后进行数组匹配，第二个数组中不在第一个数组的数据累加到第一个数组后头。当文章数目仅仅就10来篇的时候也就罢了，倘若上百篇，这么大的二维数组循环匹配太消耗资源。

想了想，还是放弃了这种方式，实在不可取。最终去在mysql手册中找了控制流程函数IF，终于解决了这个问题。

> 函数表达式：IF(expr1,expr2,expr3)  
> 如果 expr1 是TRUE (expr1 <> 0 and expr1 <> NULL)，则 IF()的返回值为expr2; 否则返回值则为 expr3。IF() 的返回值为数字值或字符串值，具体情况视其所在语境而定。

举个例子就很好理解

<pre class="lang:sh decode:true " >mysql > SELECT IF(1>2,'yes ','no');
          > 'yes'</pre>

数据库表结构设计：

id: int(11)  
title: varchar(255)  
istop: tinyint(1) //这个是是否置顶：1表示置顶；0表示非置顶  
topdeadline: int(10) //置顶截止时间，时间戳格式..  
&#8230;&#8230; //更多的字段

PHP中操作Mysql：

<pre class="lang:php decode:true " >//这里有额外的要求，当置顶截止时间为空时，代表永久置顶。
$timestamp = mktime(); //获取当前时间戳
//置顶为1时，截止时间大于当前时间或者 截止时间为0，定义一个字段为top为其赋值
$sql = "SELECT id, title,
IF(istop=1 AND (topdeadline>" . $timestamp . " OR topdeadline=0), 1, 0) AS top,1
FROM article ORDER BY id DESC";</pre>