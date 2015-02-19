---
title: MySQL建立临时表应用
author: 谇雨
layout: post
permalink: /mysql_temporary_table_application.html
views:
  - 382
categories:
  - MySql
  - PHP
tags:
  - MySql
  - 临时表
---
有的时候我们的数据表结构是这样的

currentdate counts  
2011-07-12 15  
2011-07-13 35  
2011-07-14 43  
&#8230;  
2011-07-24 34  
2011-07-25 12  
2011-07-28 25  
2011-07-29 34  
&#8230;  
2011-08-16 45  
2011-08-17 85  
2011-08-18 23

称这个表为A表，这是我们需要统计出A表 2011-07-01 到 2011-08-31 这两个月的每天的counts值。但是我们发现2011-07-01到2011-08-01， 2011-07-26到2011-07-27，2011-08-19到2011-08-31，这些地方是空的，也就是断行了，没有数据的，所以说如果要给这些地方返回 counts=0 在PHP中处理就显得非常的麻烦。  
然而我们就可以在MySQL中创建一个临时表，该表内里面的的信息为

currentdate  
2011-07-01  
2011-07-02  
2011-07-03  
&#8230;  
2011-08-29  
2011-08-30  
2011-08-31

称这个表为B表，因此只要A右链接B表，就能返回新的数据结果集。而且Mysql也提供在内存中创建临时表，但mysql链接释放的时候这个表也消失了。这不失为一个好办法，但是我没有测试过读写效率。

<pre class="lang:php decode:true " title="建立临时内存表" >//Type=heap
$this->DB->query("CREATE TEMPORARY TABLE IF NOT EXISTS tmp_table
(tmp_currentdate varchar(10) NOT NULL) TYPE = HEAP");
//设置插入数据用的SQL语句
$insertSql = "INSERT INTO tmp_table(tmp_currentdate) VALUES";
//插入数据
$this->startTime = mktime(0, 0, 0 ,7, 1, 2011);
$this->endTime   = mktime(0, 0, 0 ,9, 1, 2011);
$this->days      = ($this->endTime - $this->endTime) + 1;
for ($i=0; $idays; $i++) {
  $tmpDate = date('Y-m-d', $this->startTime + 60 * 60 * 24 * $i);
  $insertSql .= "('" . $tmpDate ."'),";
}
$insertSql = substr($insertSql,0 , strlen($insertSql)-1);

$this->DB->query($insertSql);

//临时表建立完成，等于B表
</pre>

<pre class="lang:tsql decode:true " >SELECT tmp_currentdate AS currentdate, IF(counts IS NULL,0,counts) AS counts
FROM
  (SELECT currentdate, counts FROM stat) AS A
RIGHT JOIN
  tmp_table AS TMP
ON A.currentdate=TMP.tmp_currentdate</pre>

就能返回出

currentdate counts  
2011-07-01 0  
2011-07-02 0  
&#8230;  
2011-07-11 0  
2011-07-12 15  
2011-07-13 35  
2011-07-14 43  
&#8230;