---
title: 关于设计表时应该注意的问题
author: 谇雨
layout: post
permalink: /about-design-table-attention-question.html
views:
  - 67
categories:
  - MySql
tags:
  - MySql
  - 设计表
---
<p style="text-indent:2em;">
  转载自:<a href="http://ourmysql.com/archives/373">http://ourmysql.com/archives/373</a>
</p>

<p style="text-indent:2em;">
  如有错误欢迎大家指出。这段时间在家里，做了点修正。
</p>

1、慎重选择表名。  
有两种选择：  
按照多数开发语言的命名规则。比如（myCustomer）。  
按照多数开源思想命名规则。比如（my_customer）。  
按照咱们中国人的思想。比如（我的客户）。  
第一种有个缺点，很容易忘掉大写的字母。  
第二种则比较好，每个WORD间用下划线连接，避免遗忘。  
第三种建议不要用，虽然很好记。不觉得解析这个表的时候还需要编码转化吗？我个人理解，大家可以补充。 

<!--more-->

2. 关于编码的设定。  
A．GBK/GB2312.（适用于纯中文存储）。  
B．UTF8.（适用于中英文混合存储）。  
C．LATIN1。（适用于纯英文存储）。  
D. 其他的。 

3. 关于表引擎的选择。  
A.MYISAM.（很多人说她的表级锁定会带来好多问题，其实只要设计好对应的表以及写好对应的SQL查询就没有那么大的问题。）  
B.INNODB.（如果要用到事务，选择她不会错。至于多数人讲的MASTER/SLAVE结构上用INNODB在MASTER的选择是否正确，就要看你怎么用了。不能一味的疯狂使用INNODB。除非你想要确保非常高可用性，）  
C.CSV. （以前我写过文章，关于这个引擎。个人觉得最主要的是来存储少量数据以及从EXCEL到MYSQL的转换方面会很有用。当然只要涉及到规则数据的导入，她就可以办到。）  
D.BLACKHOLE. （觉得最完美的用处在于MASETR/SLAVE上面，并且MASTER是一个临时的专门负责写的机器。不过缺点也很多，会与MYISAM或者INNODB或者其他的引擎有所冲突，这点自己要做个权衡）。  
E.MEMORY. （应该说是MYISAM的兄弟了。不过在读内存总比读磁盘的速度要快。不过要注意，它不支持动态数据类型）。  
F.FEDERATED. （典型的分布式引擎。我以前文章中有介绍。）  
G.NDB。（网络版存储引擎。因为Replication 总是有延迟，所以如果系统容不得任何延迟，就用这个吧。）  
H.FOLCON。（6.0后用来代替INNODB的引擎。）  
I.其他旧的以及新开发的引擎具体介绍：<http://dev.mysql.com/doc/refman/6.0/en/storage-engines.html>）。 

4. 关于属性数据类型的选择。  
A. INT（一个字节的TINYINT，两个字节的SMALLINT，三个字节的MEDIUMINT，四个字节的INT，8个字节的BIGINT。记住：UNSIGNED不管你定义或者不定义，都不影响内部的存储字节大小）  
B. 少于10个字符用CHAR是在合适不过了。（不过要记住在MEMORY引擎里面会自动把VARCHAR转化为CHAR）  
C. 我一般用DECIMAL或者NUMERIC来代替FLOAT 或者DOUBLE。因为老板要求精确的数字。如果不要求精确的，那就用FLOAT吧。速度快，占空间小。（DECIMA、FLOAT(P)是动态存储。比如:DECIMAL(10,2)占用5个字节。FLOAT占4个字节，）  
D. BLOB，TEXT,VARCHAR（一般存放文章内容，特别是新闻网站。需要的字节数是所存储的字符长度+1。记住BLOB和VARCHAR是TEXT和CHAR的BINARY类型）  
E. ENUM（在一定范围内绝佳的代替VARCHAR和CHAR的工具，因为她只占一到两个字节。）  
F. 时间和日期类型（占3个字节的DATE，8个字节的DATETIME，4个字节的TIMESTAMP，3个字节的TIME，1个字节的YEAR。）。如果要存储比如&#8217;1983&#8217;这样的年份，用YEAR明显比VARCHAR或者CHAR要节省空间。因为后者要占5个字节。  
G. BOOLEAN（用来存储YES或者NO之类的值，占用一个字节。）  
H. 关于自增字段。目前我们的项目中涉及到好多ORDER BY RAND()操作。此类语句在数据库并发大的时候会造成CPU严重阻塞，持续产生数据库死锁！解决此类问题最好的办法就是利用自增字段，用程序随即生成数字序列，或者在数据库端随即生成数字序列。  
I. 关于ZEROFILL。非常好用的前置填补0的存储，而不是用用对应个数的空串来代替。在需要前置补零的操作中INT ZEROFILL可以用来代替CHAR或者VARCHR。 

5. 关于默认值。  
A. 在5.0之后，只要设定字段为NOT NULL，系统自动给出默认值。对应CHAR->&#8221;,INT->0,BOOLEAN->0等等。  
B. 在5.0之前的版本，需要手动指定默认值，否则会出现一定的异常。到时候查都不好查了。 

6. 关于多数据库建立。  
A. 应该把对应的业务放在各自不同的数据库里，而不是所有业务放到一个库里面。  
B. 数据库的命名和表命名一样。 

7. 关于索引。  
A. 设计表初期尽量考虑到应该建立的索引。所有建立的索引一定要测试一下，看是否有必要，否则会翻倍的减少写数据的性能。  
B. 对于只有存储0或者1的列，尽量干掉索引，单独分出两个表。一个代替0，另外一个代替1。或者在一个字段里面用EMUM或者CHAR(0)或者CHAR(1)来代替。 

<p style="text-indent:2em;">
  PS： 最后一个要值得注意的，就是尽量所有的字段用NOT NULL。虽然MYSQL可以对NULL列进行索引，不过我不建议。
</p>