---
title: Ubuntu下安装PHP环境(Apache+MySQL)
author: 谇雨
layout: post
permalink: /ubuntu-php-setup.html
views:
  - 66
categories:
  - Linux
---
1. 直接打开终端输入下面命令自动下载安装运行环境

sudo apt-get install apache2

sudo apt-get install mysql-server

sudo apt-get install php5-common

sudo apt-get install php5-gd

sudo apt-get install php5-mysql

sudo apt-get install libapache2-mod-php5

2. 配置环境

a).配置php5

sudo gedit /etc/php5/apache2/php.ini  
<!--more-->

  
允许 mysql 和 gd 模块，检查文件最后是否包含下面的代码，如果没有添加上。（默认是在配置文件最后有添加的，检查一下以防万一）

extension=mysql.soextension=gd.so3. 

b). 配置Apache

sudo gedit /etc/apache2/apache2.conf

b). 配置 mysql，让它支持其它客户端访问，如果你不需要就不用修改。

sudo gedit /etc/mysql/my.cnf

查找文件：skip-networking

修改为：#skip-networking

保存并关闭文件。

重新启动 mysql

sudo /etc/init.d/mysql restart

3. 运行、停止和重启Apache服务

sudo /usr/sbin/apache2ctl start

sudo /usr/sbin/apache2ctl stop

sudo /usr/sbin/apache2ctl restart

4. 测试

为检查你的PHP安装状态，新建PHP文件：

sudo gedit /var/www/test.php

在上述文件中插入下面这行代码：

<?php phpinfo(); ?>

在Web浏览器中查看此页面，通过 http://localhost/testphp.php 或者其他