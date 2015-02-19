---
title: Lnmp安装MongoDB以及PHP扩展
author: 谇雨
layout: post
permalink: /lnmp-install-mongodb-and-php-extension.html
views:
  - 52
categories:
  - NoSql
tags:
  - lnmp
  - mongodb
  - php
---
MongoDB安装

<pre class="lang:sh decode:true " >wget -c  http://fastdl.mongodb.org/linux/mongodb-linux-i686-2.4.1.tgz                 
tar zxvf mongodb-src-r2.4.1.tar.gz                 
mv mongodb-linux-i686-2.4.1 /usr/local/mongodb/
/usr/local/mongodb/bin/mongod --dbpath=/usr/local/mongodb/data/ --logpath=/usr/local/mongodb/dblogs --fork</pre>

启动以后可以查看mongodb进程树

<pre class="lang:sh decode:true " >pstree -p |grepmongod</pre>

PHP MongoDB 扩展

<pre class="lang:sh decode:true " >wget http://pecl.php.net/get/mongo-1.3.6.tgz             
/usr/bin/phpize
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install
/usr/local/php/etc/php.ini             
extension=mongo.so;</pre>

关闭注意  
这里禁止使用 kill -9 PID 关闭mongodb进程，会导致mongod.lock导致再一次无法开启mongodb，必须删除mongod.lock再能开启  
可以使用pkill mongod 或者 killall mongod 来结束mongodb进程