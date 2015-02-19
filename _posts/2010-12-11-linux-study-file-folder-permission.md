---
title: Linux学习笔记 – 文件与文件夹权限的理解
author: 谇雨
layout: post
permalink: /linux-study-file-folder-permission.html
views:
  - 156
categories:
  - Linux
tags:
  - Linux
  - 文件
  - 文件夹
  - 权限
---
<p style="text-indent: 2em;">
  之前很早就了解过Linux上的777权限，一般都是设置在文件上，今天看了教程又详细理解了一下Linux的文件、文件夹权限。
</p>

<p style="text-indent: 2em;">
  虽然Linux下的文件和文件夹的权限都用r、w、x表示，但其意义却是不同的。因此整理了一张表。
</p>

<table width="80%" border="1" align="center" cellpadding="0" cellspacing="0" style="margin:auto;">
  <tr>
    <th width="20%" style="padding:10px;" align="center">
      &nbsp;
    </th>
    
    <th width="35%" style="padding:10px;" align="center">
      文件
    </th>
    
    <th width="45%" style="padding:10px;" align="center">
      文件夹
    </th>
  </tr>
  
  <tr>
    <th style="padding:10px;">
      r(read)
    </th>
    
    <td style="padding:10px;">
      可以读取文件的内容
    </td>
    
    <td style="padding:10px;">
      读取文件夹内容列表，但如果没有x权限，就只能看到文件名而无法查看其他内容(大小、权限等)
    </td>
  </tr>
  
  <tr>
    <th style="padding:10px;">
      w(write)
    </th>
    
    <td style="padding:10px;">
      可以编辑、修改文件的内容(注：没有删除文件和改名的权限)
    </td>
    
    <td style="padding:10px;">
      可以对新修、修改、移动、重命名文件(夹)
    </td>
  </tr>
  
  <tr>
    <th style="padding:10px;">
      x(execute)
    </th>
    
    <td style="padding:10px;">
      该文件可以被执行
    </td>
    
    <td style="padding:10px;">
      可以进入文件夹
    </td>
  </tr>
</table>

<!--more-->

<p style="text-indent: 2em;">
  这里需要注意的是，由于目录将其下的所有文件看作是它的内容，因此文件的改名与删除权限是被Linux归入该文件父目录的w权限中的。当用户改名或删除文件(夹)时，执行的是对它上一级目录的w操作，也因为这样，删除文件 (夹)可以无视它自身的权限设置。
</p>

因此Linux的根目录(/)其实就是一个文件夹，  
<coolcode lang="php" linenum="off">[lancer@localhost ~]$ ls -ld /  
drwxr-xr-x 26 root root 4096 12-11 11:54  
[lancer@localhost ~]$ mkdir /test  
mkdir: 无法创建目录 ‘/test’: 权限不够</coolcode>  
由此可见根目录(/)只有root用户，其他人均无权增删改  
因此大多数源码在要求安装在Linux服务器上时要求upload,data,install等文件夹开启777(所有)的权限