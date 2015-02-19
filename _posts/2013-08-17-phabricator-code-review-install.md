---
title: 安装Phabricator代码审核工具
author: 谇雨
layout: post
permalink: /phabricator-code-review-install.html
views:
  - 268
categories:
  - Linux
tags:
  - phabricator
  - 代码审核
---
官方安装文档http://www.phabricator.com/docs/phabricator/article/Installation_Guide.html#installation-requirement

Centos 选择Redhat系列  
wgethttp://www.phabricator.com/rsrc/install/install_rhel-derivs.sh  
$ cd somewhere/ # pick some install directory  
somewhere/ $ git clone git://github.com/facebook/libphutil.gitsomewhere/  
$ git clone git://github.com/facebook/arcanist.gitsomewhere/  
$ git clone git://github.com/facebook/phabricator.git

配置自己的web指定的服务器。

根据提示设置mysql用户名密码，导入数据  
phabricator/ $ ./bin/storage upgrade 

通过http访问如果提示  
[Exception] Failed to open process  
说明proc\_open有问题，检查是否开启了php.ini中disable\_functions功能是否存在该函数，并且去掉。  
<!--more-->

  
安装pcntl.so扩展  
可以下载源码包，然后指定只编译pcntl.so  
$ wget http://br.php.net/get/php-5.3.15.tar.gz/from/this/mirror)  
$ sudo su  
$ tar -zxvf php-5.3.15.tar.gz  
$ cd php-5.3.15/ext/pcntl/  
$ phpize && ./configure && make install  
$ echo &#8220;extension=pcntl.so&#8221; >> /etc/php.ini  
查看是否模块已经加载  
$ php -m | grep pcntl