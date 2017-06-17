---
title: 关于无限级分类的几种数据库设计分析
author: 谇雨
layout: post
permalink: /about-unlimited-class-database-analyse.html
views:
  - 1338
categories:
  - MySql
  - PHP
tags:
  - 左右值
  - 数据库
  - 无限级
  - 递归
---
第一种方案：

<p style="text-indent:2em">
  使用递归算法，也是使用频率最多的，大部分开源程序也是这么处理，不过一般都只用到四级分类。这种算法的数据库结构设计最为简单。category表中一个字段id，一个字段fid(父id)。这样可以根据WHERE id = fid来判断上一级内容，运用递归至最顶层。
</p>

<p style="text-indent:2em">
  分析：通过这种数据库设计出的无限级，可以说读取的时候相当费劲，所以大部分的程序最多3-4级分类，这就足以满足需求，从而一次性读出所有的数据，再对得到数组或者对象进行递归。本身负荷还是没太大问题。但是如果分类到更多级，那是不可取的办法。<br /> 这样看来这种分类有个好处，就是增删改的时候轻松了&#8230;然而就二级分类而言，采用这种算法就应该算最优先了。
</p>

第二种方案：

<p style="text-indent:2em">
  设置fid字段类型为varchar，将父类id都集中在这个字段里，用符号隔开，比如：1,3,6<br /> 这样可以比较容易得到各上级分类的ID，而且在查询分类下的信息的时候，<br /> 可以使用：SELECT * FROM category WHERE pid LIKE &#8220;1,3%&#8221;。
</p>

<!--more-->

<p style="text-indent:2em">
  分析：相比于递归算法，在读取数据方面优势非常大，但是若查找该分类的所有 父分类 或者 子分类 查询的效率也不是很高，至少也要二次query，从某种意义看上，个人觉得不太符合数据库范式的设计。倘若递增到无限级，还需考虑字段是否达到要求，而且在修改分类和转移分类的时候操作将非常麻烦。
</p>

第三种方案：

<p style="text-indent:2em">
  使用预排序遍历树算法，也叫左右值算法。特别适用于多层数据结构设计，像Windows，linux下文件夹结构。在这种数据结构中，设置每个节点的左右值为唯一。通过左右值把所有节点连接起来。
</p>

<table width="400" border="0" align="center" cellpadding="0" cellspacing="0">
  <tr>
    <td>
      数据结构：
    </td>
    
    <td>
      &nbsp;
    </td>
  </tr>
  
  <tr>
    <td>
      id:
    </td>
    
    <td>
      类别id，主键
    </td>
  </tr>
  
  <tr>
    <td>
      name:
    </td>
    
    <td>
      类名
    </td>
  </tr>
  
  <tr>
    <td>
      lft:
    </td>
    
    <td>
      左值
    </td>
  </tr>
  
  <tr>
    <td>
      rgt:
    </td>
    
    <td>
      右值
    </td>
  </tr>
</table>

<p style="text-indent:2em">
  分析：这种数据结构的设计完全可以摒弃掉fid,orderview这些字段，按顺序遍历左右值到达查询效果，这种查询速度非常之快，而且只要一次查询就能得到想要的结果，可以轻易的查询节点是否存在子节点，父节点的路径等。但同样面临的在增删改的时候操作比较复杂。这种预排序遍历树算法的核心就是大量牺牲了写的性能来换取读取的性能。而且完全可以扩展到无限级应用。
</p>