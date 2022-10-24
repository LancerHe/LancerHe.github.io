---
title: 在Linux下实现Mysql远程访问
author: 谇雨
layout: post
permalink: /linux-mysql-remoteaccess.html
views:
  - 50
categories:
  - PHP
  - 站长日记
tags:
  - Linux
  - MySql
  - 远程访问
---
[<img class="alignnone size-full wp-image-748" title="remote-mysql" src="{{ site.url }}/uploads/2012/12/remote-mysql.jpg" alt="" width="590" height="150" />][1]

### 步骤一 激活Mysql配置文件my.cnf中的远程访问

位置：

> /etc/mysql/my.cnf (Debian linux)  
> /etc/my.cnf （Red Hat Linux/Fedora Linux)  
> /var/db/mysql/my.cnf (FreeBSD)

用VI打开，查找到bind-address这项，默认情况是127.0.0.1  
把他注释掉，或者改成数据库主机所在IP地址。  
比如 bind-address = 65.212.65.12  
<!--more-->

  
[<img class="alignnone size-full wp-image-749" title="remote-mysql-2" src="{{ site.url }}/uploads/2012/12/remote-mysql-2.jpg" alt="" width="363" height="265" />][2]

### 步骤二 授权帐户具有远程访问属性

首先登陆在数据库所在Linux上用Root权限登陆Mysql。

<pre class="lang:default decode:true " >use mysql;
select host, user from mysql;

[<img class="alignnone size-full wp-image-751" title="remote-mysql-2" src="{{ site.url }}/uploads/2012/12/remote-mysql-2.gif" alt="" width="370" height="300" />][3]

正常情况下是只有三个root用户，他们的host分别是

*   127.0.0.1
*   ::1
*   localhost

如果我们其中127.0.0.1改为我们所需要的ip地址，比如：218.51.21.21，那么用这台IP的机子就能实现远程登陆mysql进行管理。这里要用到用户授权的语法。

> grant all on \*.\* to username@&#8217;ip&#8217; identified by &#8220;password&#8221;;

由此我们就可以新建一个超级管理员的帐户：

<pre class="lang:default decode:true " >mysql > grant all privileges on *.* to admin@localhost identified by '123456' with grant option;
mysql > grant all privileges on *.* to admin@'%' identified by '123456' with grant option;


其中第二句中&#8217;%&#8217;表示通配符，任何IP都能通过admin帐户远程访问mysql。  
建立好之后我们再看看

<pre class="lang:default decode:true " >select host, user from user;


是不是发现有两个新的用户名是admin，这时我们就可以开启远程访问了。

 [1]: {{ site.url }}/uploads/2012/12/remote-mysql.jpg
 [2]: {{ site.url }}/uploads/2012/12/remote-mysql-2.jpg
 [3]: {{ site.url }}/uploads/2012/12/remote-mysql-2.gif