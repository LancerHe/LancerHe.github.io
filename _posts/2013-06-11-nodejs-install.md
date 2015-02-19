---
title: 'Nodejs &#8211; 初学安装'
author: 谇雨
layout: post
permalink: /nodejs-install.html
views:
  - 45
categories:
  - Nodejs
tags:
  - nodejs
  - 初学
  - 安装
---
快速安装， 到Nodejs官网下载最新的安装包。

<pre class="lang:sh decode:true">wget http://nodejs.org/dist/v0.9.0/node-v0.9.0.tar.gz
tar zxvf node-v0.9.0.tar.gz

cd node-v0.9.0
./configure --prefix=/usr/local/node
make && make install

vi /etc/profile 
#安装完成后，配置环境变量
#export PATH USER上面添加
#set for nodejs 
export NODE_HOME=/usr/local/node
export PATH=$NODE_HOME/bin:$PATH

#保存退出，使刚才的配置生效 
source /etc/profile</pre>

&nbsp;

老规矩，创建一个server.js来完成hello world！

<pre class="lang:js decode:true">var http = require('http');
http.createServer(function (request, response) {
    console.log("Request received.");
    response.writeHead(200, {'Content-Type': 'text/plain'});
    response.write('Hello World');
    response.end();
}).listen(8888);
console.log('Server running.');</pre>

&nbsp;

开启服务

<pre class="lang:sh decode:true">node server.js</pre>

会看到Linux控制台上打印出Server running.

我们再用浏览器访问下：

http://192.168.1.102:8888/

&nbsp;

浏览器上打印出：  
Hello World  
&nbsp;

而Linux控制台上出现两次  
Request received.  
Request received.  
&nbsp;

出现两次请求信息是因为大部分服务器访问你的http://192.168.1.102:8888/时会读取http://192.168.1.102:8888/favicon.ico导致的。