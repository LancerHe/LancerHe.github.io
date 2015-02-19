---
title: sphinx/coreseek安装
author: 谇雨
layout: post
permalink: /sphinx-coreseek-instal.html
views:
  - 55
categories:
  - Linux
tags:
  - coreseek
  - sphinx
  - 安装
---
Sphinx 官方版本安装  
wget http://sphinxsearch.com/files/sphinx-2.0.3-release.tar.gz  
tar zxvf sphinx-2.0.3-release.tar.gz  
./configure &#8211;prefix=/usr/local/sphinx &#8211;with-mysql  
make && make install  
cd /usr/local/sphinx  
cp etc/sphinx-min.conf.dist etc/sphinx.conf  
测试数据库，新建test数据库，导入etc/example.sql做测试  
#修改mysql帐户密码  
vim etc/sphinx.conf  
#建立索引  
bin/indexer -c etc/sphinx.conf test1  
#查询  
bin/search -c etc/sphinx.conf test  
#开启守护进程  
bin/searchd -c etc/sphinx.conf

<!--more-->

  
但是官方不支持中文分词，硬伤……  
支持中文方案：  
1. 安装 Sphinx-For-Chinese  
wget http://sphinx-for-chinese.googlecode.com/files/sphinx-for-chinese-2.1.0-dev-r3361.tar.bz2  
tar -xvf sphinx-for-chinese-2.0.2-dev-r2894.tar.gz  
$ cd sphinx-for-chinese-2.0.2-dev-r2894  
$ ./configure &#8211;prefix=/usr/local/sphinx-for-chinese  
&#8211;prefix 指定安装路径  
&#8211;with-mysql 编译mysql支持  
&#8211;with-pgsql 编译pgsql支持  
$ make  
$ make install

配置中文支持  
wget http://sphinx-for-chinese.googlecode.com/files/xdict_1.1.tar.gz  
$ tar -xvf xdict_1.1.tar.gz  
$ /usr/local/sphinx-for-chinese/bin/mkdict xdict\_1.1.txt xdict #从xdict\_1.1.txt生成xdict文件，xdict_1.1.txt文件可以根据需要进行修改  
$ cp xdict /usr/local/sphinx-for-chinese/etc/

修改sphinx.conf索引配置文件，在索引配置项中添加以下两项  
charset_type = utf-8  
chinese_dictionary = /usr/local/sphinx-for-chinese/etc/xdict

2. 按照Coreseek  
具体参照： http://www.coreseek.cn/products-install/install\_on\_bsd_linux/  
$ wget http://www.coreseek.cn/uploads/csft/4.0/coreseek-4.1-beta.tar.gz  
$ tar xzvf coreseek-4.1-beta.tar.gz  
$ cd coreseek-4.1-beta/mmseg-3.2.14/  
$ ./bootstrap #输出的warning信息可以忽略，如果出现error则需要解决  
$ ./configure &#8211;prefix=/usr/local/mmseg3  
$ make && make install  
$ cd ..

##安装coreseek  
$ cd csft-3.2.14 或者 cd csft-4.0.1 或者 cd csft-4.1  
$ sh buildconf.sh #输出的warning信息可以忽略，如果出现error则需要解决  
$ ./configure &#8211;prefix=/usr/local/coreseek &#8211;without-unixodbc &#8211;with-mmseg &#8211;with-mmseg-includes=/usr/local/mmseg3/include/mmseg/ &#8211;with-mmseg-libs=/usr/local/mmseg3/lib/ &#8211;with-mysql ##如果提示mysql问题，可以查看MySQL数据源安装说明  
$ make && make install  
$ cd ..

PHP安装sphinx扩展  
#先安装sphinxclient  
cd sphinx-2.0.3-release/api/libsphinxclient  
vim sphinxclient.c  
#找到 void sock_close ( int sock );  
改为 static void sock_close ( int sock );  
./configure &#8211;prefix=/usr/local/sphinxclient  
make && make install

wget http://pecl.php.net/get/sphinx-1.3.0.tgz  
tar zxvf sphinx-1.3.0.tgz  
cd sphinx-1.3.0  
/usr/local/php/bin/phpize  
./configure &#8211;with-php-config=/usr/local/php/bin/php-config &#8211;with-sphinx=/usr/local/sphinxclient  
make && make install

在php.ini加入sphinx.so

这里最后选择Coreseek  
更新索引，可以放在Crontab中执行，每天自动更新  
/usr/local/coreseek/bin/indexer &#8211;rotate -c /usr/local/coreseek/etc/sphinx.conf &#8211;all