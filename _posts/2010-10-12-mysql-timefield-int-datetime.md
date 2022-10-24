---
title: MySQL时间字段究竟使用INT还是DateTime？
author: 谇雨
layout: post
permalink: /mysql-timefield-int-datetime.html
views:
  - 149
categories:
  - MySql
tags:
  - DateTime
  - int
  - MySql
  - 时间
---
本文转载自：<http://ourmysql.com/archives/547>

本文在如下环境中测试效果：  
#Windows XP  
#PHP Version 5.2.9  
#MySQL Server 5.1  
<!--more-->

<pre class="lang:tsql decode:true " >#第一步、创建一个表date_test（非定长、int时间）

CREATE TABLE `test`.`date_test` (
`id` INT NOT NULL AUTO_INCREMENT ,
`start_time` INT NOT NULL ,
`some_content` VARCHAR( 255 ) NOT NULL ,
PRIMARY KEY ( `id` )
) ENGINE = InnoDB;

#第二步、创建第二个表date_test2（定长、int时间）

CREATE TABLE `test`.`date_test2` (
`id` INT NOT NULL AUTO_INCREMENT ,
`start_time` INT NOT NULL ,
`some_content` CHAR( 255 ) NOT NULL ,
PRIMARY KEY ( `id` )
) ENGINE = InnoDB;

#第三步、创建第三个表date_test3（varchar、datetime时间）

CREATE TABLE `test`.`date_test3` (
`id` INT NOT NULL AUTO_INCREMENT ,
`start_time` DATETIME NOT NULL ,
`some_content` VARCHAR( 255 ) NOT NULL ,
PRIMARY KEY ( `id` )
) ENGINE = InnoDB;

#第四步、创建第四个表date_test3（char、datetime时间）

CREATE TABLE `test`.`date_test4` (
`id` INT NOT NULL AUTO_INCREMENT ,
`start_time` DATETIME NOT NULL ,
`some_content` CHAR( 255 ) NOT NULL ,
PRIMARY KEY ( `id` )
) ENGINE = InnoDB;

现在我们开始做测试，环境是php，先向各个表插入一百万条数据。插入的时候分200次，每次进库5000条。

表一执行记录：页面运行时间: 26.5997889042 秒，插入的时候发现一个有趣的现象：SELECT count(id) FROM \`date\_test\` WHERE 1 的结果是100w,而直接select * from \`date\_test\`却是1,000,374条结果。（后来看到这是一个可能接近的值，请参看MySQL FAQ 3.11）。  
表二执行记录：页面运行时间: 62.3908278942 秒，这次记录是1,000,066条。  
表三执行记录：页面运行时间: 30.2576560974 秒，这次的是1,000,224条。  
表四执行记录：页面运行时间: 67.5393900871 秒，这次的是：1,000,073条。

现在把四个表的start_time字段一一加上索引。

测试四个表的更新，分别update 100条记录，并记录时间:  
表一：页面运行时间: 2.62180089951 秒（非定长，int时间）  
表二：页面运行时间: 2.5475358963 秒（定长，int时间）  
表三：页面运行时间: 2.45077300072 秒（varchar,datetime时间）  
表四：页面运行时间: 2.82798409462 秒（char,datetime时间）

测试四个表的读取，分别select 100条随机记录，以主键id为条件查询，并记录时间：  
表一：页面运行时间: 0.382651090622 秒（非定长，int时间）  
表二：页面运行时间: 0.542181015015 秒（定长，int时间）  
表三：页面运行时间: 0.334048032761 秒（varchar,datetime时间）  
表四：页面运行时间: 0.506206989288 秒（char,datetime时间）

测试四个表的读取，分别select 10条随机记录，以star_time为条件查询，并记录时间：  
表一：页面运行时间: 30.1972880363 秒（非定长，int时间）  
表二：页面运行时间: 65.1926910877 秒（定长，int时间）  
表三：页面运行时间: 39.7210869789 秒（varchar,datetime时间）  
表四：页面运行时间: 70.4632740021 秒（char,datetime时间）

因为量比较小，所以我们默认即使是微小的变化，也是有意义的。

结论：  
大数据量下，如果存在大量的select * from table where 时间>XX这样的查询，在MySQL5.1时使用int换datetime是有意义的。