---
title: 使用nginx-http-concat自动合并静态资源，加速你的网站请求速度
author: 谇雨
layout: post
permalink: /nginx-combine-static-resources-make-site-more-fast.html
views:
  - 508
categories:
  - Linux
tags:
  - Nginx
  - nginx-http-concat
  - 加速网站
  - 合并资源
---
首先了解一下 nginx-http-concat，他是一个淘宝的开源Nginx模块，是一个能把多个CSS和JS合并成一个请求的Nginx模块，对于Web性能优化非常有意义。

Github地址：<a href="https://github.com/alibaba/nginx-http-concat" target="_blank">https://github.com/alibaba/nginx-http-concat</a>，

先看看淘宝用起来是什么样的，访问淘宝网主页，查看源代码可以看到类似的这样的style/script链接

<pre class="lang:xhtml decode:true">&lt;link rel="stylesheet" href="//g.tbcdn.cn/??tb/global/2.1.6/global-min.css,tb/tb-fp/1.2.3/style-min.css?t=20130912"&gt;
&lt;script src="//g.tbcdn.cn/??kissy/k/1.3.1/seed-min.js,tb/global/2.1.6/global-min.js,tb/tb-fp/1.2.3/core-min.js?t=20130912"&gt;&lt;/script&gt;</pre>

是不是很神奇，只需要一个请求，就可以把需要的CSS/JS文件通过合并的方式把他输出成一个文件(注意，虽然淘宝有min格式的文件，但是这里它仅仅是合并多个文件，而不会自动的对其压缩打包文件)

首先我们先从Git上下载安装它

<pre class="lang:sh decode:true">#下载
$ git clone git://github.com/alibaba/nginx-http-concat.git

#移动目录
$ mv nginx-http-concat /usr/local/src/nginx-http-concat</pre>

查看原始Nginx版本，这很重要，因为我们需要安装同一个版本来升级数据

<pre class="lang:sh decode:true">#查看版本号以及配置信息(目录/模块)
$ /usr/local/nginx/sbin/nginx -V
nginx version: nginx/1.3.1
TLS SNI support disabled
configure arguments: --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_gzip_static_module</pre>

根据查询的版本号下载对应版本的nginx，可以到官方下载指定版本：<a href="http://nginx.org/download/" target="_blank">http://nginx.org/download/</a>

我这里使用的是1.3.1

<pre class="lang:sh decode:true ">$ wget nginx-1.3.1.tar.gz
$ tar zxvf nginx-1.3.1.tar.gz
$ cd nginx-1.3.1
#根据上面-V的信息 加入concat模块所在路径 (--add-module=/usr/local/src/nginx-http-concat) 进行编译
$ ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_gzip_static_module --add-module=/usr/local/src/nginx-http-concat</pre>

make之前备份配置文件，防止意外

<pre class="lang:default decode:true " >$ cp -r /usr/local/nginx/conf /root/nginxconf
#编译安装
$ make && make install</pre>

接下来就是配置你的静态服务器conf文件

<pre class="lang:default decode:true " >server {
        listen       80;
        server_name static.dexphp.loc;
        index index.html index.htm;
        root  /mnt/siteroot/static.dexphp.com;
                
        location /static/css/ {
                concat on;
                concat_max_files 20; //最大合并文件数量是20个
        }

        location /status {
                stub_status on;
                access_log   off;
        }

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|js)$ {
                expires      off;
        }

        error_log   /mnt/siteroot/wwwlogs/static.dexphp.loc.error.log;
        access_log  /mnt/siteroot/wwwlogs/static.dexphp.loc.access.log;
}
</pre>