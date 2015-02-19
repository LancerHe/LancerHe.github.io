---
title: windows下apache设置域名创建虚拟目录
author: 谇雨
layout: post
permalink: /apache-domain-virtual-directory.html
views:
  - 468
categories:
  - Apache
tags:
  - Apache
  - 域名
  - 虚拟目录
---
打开apache下的配置文件httpd.conf

找到NameVirtualHost *:80  去掉注释符，保证前面没有#，开始建立虚拟目录.

<pre class="lang:apache decode:true " >&lt;VirtualHost *:80&gt;
DocumentRoot "D:\www\guestbook"
ServerName local.guestbook.com
&lt;/VirtualHost&gt;

&lt;VirtualHost *:80&gt;
DocumentRoot "D:\www\cart"
ServerName local.cart.com
&lt;/VirtualHost&gt;

&lt;VirtualHost *:80&gt;
DocumentRoot "D:\www\zone"
ServerName local.zone.com
&lt;/VirtualHost&gt;</pre>

保存配置文件，重启apache后，虚拟目录就创建好了。然后需要对域名进行强制解析转向到我们的虚拟目录。  
打开C:\WINDOWS\system32\drivers\etc文件夹下的host文件  
在最后添加上自己的本地地址localhost 或者自己的ip

<pre class="lang:sh decode:true " >localhost local.zone.com
192.168.1.100 local.guestbook.com
192.168.1.100 local.cart.com
192.168.1.100 local.crackedzone.com
192.168.1.100 local.test.com</pre>