---
title: HTTPSQS简单队列服务的应用安装
author: 谇雨
layout: post
permalink: /httpsqs_simple_queue-service.html
views:
  - 32
categories:
  - Linux
---
> 本文转自：<a href="http://blog.s135.com/httpsqs/5/1/" target="_blank">张宴老师的基于HTTP协议的轻量级开源简单队列服务</a>

最近因为工作需要才解除到队列服务，不可不说HTTPSQS是个非常高效的一个服务应用。

&nbsp;

先介绍一些队列的原理和应用：

队列（Queue）又称先进先出表（First In First Out），即先进入队列的元素，先从队列中取出。加入元素的一头叫“队头”，取出元素的一头叫“队尾”。利用消息队列可以很好地异步处理数据传送和存储，当你频繁地向数据库中插入数据、频繁地向搜索引擎提交数据，就可采取消息队列来异步插入。另外，还可以将较慢的处理逻辑、有并发数量限制的处理逻辑，通过消息队列放在后台处理，例如FLV视频转换、发送手机短信、发送电子邮件等。

&nbsp;

HTTPSQS（HTTP Simple Queue Service）是一款基于 HTTP GET/POST 协议的轻量级开源简单消息队列服务，使用 Tokyo Cabinet 的 B+Tree Key/Value 数据库来做数据的持久化存储。

&nbsp;

项目网址：<a href="http://code.google.com/p/httpsqs/" target="_blank">http://code.google.com/p/httpsqs/</a>  
使用文档：<a href="http://blog.s135.com/httpsqs/" target="_blank">http://blog.s135.com/httpsqs/</a>  
使用环境：Linux（同时支持32位、64位操作系统，推荐使用64位操作系统）

&nbsp;

服务器安装

<pre class="lang:sh decode:true">wget http://httpsqs.googlecode.com/files/libevent-2.0.12-stable.tar.gz
tar zxvf libevent-2.0.12-stable.tar.gz
cd libevent-2.0.12-stable/
./configure --prefix=/usr/local/libevent-2.0.12-stable/
make && make install
cd ../

wget http://httpsqs.googlecode.com/files/tokyocabinet-1.4.47.tar.gz
tar zxvf tokyocabinet-1.4.47.tar.gz
cd tokyocabinet-1.4.47/
./configure --prefix=/usr/local/tokyocabinet-1.4.47/
#Note: In the 32-bit Linux operating system, compiler Tokyo cabinet, please use the ./configure --enable-off64 instead of ./configure to breakthrough the filesize limit of 2GB.
#./configure --enable-off64 --prefix=/usr/local/tokyocabinet-1.4.47/
make && make install
cd ../

wget http://httpsqs.googlecode.com/files/httpsqs-1.7.tar.gz
tar zxvf httpsqs-1.7.tar.gz
cd httpsqs-1.7/
make && make install
cd ../</pre>

这里注意安装第二个tokyocabinet的时候如果是64位系统请按照注释出使用配置文件,不清楚是多少位可以用：

<pre class="lang:sh decode:true">getconf LONG_BIT
#或者
getconf WORD_BIT
#或者
file /bin/ls</pre>

安装好后，执行httpsqs -h能看到一系列帮助参数信息。

<pre class="lang:sh decode:true">httpsqs -h</pre>

创建data，设置端口为1218

<pre class="lang:sh decode:true">httpsqs -d -p 1218 -x /data0/queue</pre>

访问你的URL，如

<pre class="lang:default decode:true " >http://192.168.1.102:1218/?name=your_queue_name&amp;opt=put&amp;data=my_data</pre>

看到浏览器上出现HTTPSQS\_PUT\_OK，表示队列写入成功  
访问URL

<pre class="lang:default decode:true " >http://192.168.1.102:1218/?name=your_queue_name&opt=get</pre>

看到浏览器上出现my\_data，表示取出your\_queue\_name一个数据my\_data  
再次刷新发现出现HTTPSQS\_GET\_END，表示队列中已经没有数据了。