---
title: 让sql语句不排序,按照in语句的顺序返回结果
author: 谇雨
layout: post
permalink: /sql-order-by-in-result.html
categories:
  - MySql
tags:
  - in
  - MySql
---

MySQL:

    SELECT * FROM EVENT WHERE eventId IN(443,419,431,440,420,414,509)  ORDER BY INSTR(',443,419,431,440,420,414,509,',CONCAT(',',eventId,','))

Oracle:

    SELECT NAME FROM order WHERE oderid IN (111,222,333,444,555,666) ORDER BY INSTR('111,222,333,444,555,666',orderid)