---
title: Vmware上通过NAT方式与Linux通信，FTP访问小结
author: 谇雨
layout: post
permalink: /vmware-nat-linux-ftp-putty.html
views:
  - 68
categories:
  - Linux
  - 站长日记
tags:
  - ftp
  - Linux
  - nat
  - VMware
---
[<img src="{{ site.url }}/uploads/2012/12/vmware.jpg" alt="" title="vmware" width="590" height="150" class="alignnone size-full wp-image-757" />][1]

> 本文主要记录Vmware虚拟机上通过NAT方式与Linux与主机进行相互通信的问题，通过FTP上传文件，另外介绍putty工具的使用。

在Vmware上使用桥接是最容易完成通信的工作，但是往往有的时候我们只有一个IP，所以选择NAT方式是最好的一种通信方式。  
<!--more-->

  
首先我们要设置NAT方式所用的VMnet8的相关信息，从菜单的Edit->Virtual Network Editor进入。

这里我的机子IP为：192.168.0.222，

所以设置我的一个虚拟机子网段为192.168.100.0

[<img class="alignnone size-full wp-image-738" title="vmware-1" src="{{ site.url }}/uploads/2012/12/vmware-1.jpg" alt="" width="614" height="569" />][2]

点击打开NAT Setting，

设置子网的网关：192.168.100.2

设置映射到本机的端口号，由于我的80端口有其他用途，所以设置一个其他的端口8080，

设置虚拟机Linux上的端口为80，

设置好虚拟机Linux的IP为：192.168.100.100

[<img class="alignnone size-full wp-image-739" title="vmware-2" src="{{ site.url }}/uploads/2012/12/vmware-2.jpg" alt="" width="516" height="506" />][3]

上述操作完毕后，就进入Linux系统吧，首先根据刚才的设置，找到文件

> /etc/network/interfaces

<pre class="lang:sh decode:true " >~$ vi /etc/network/interfaces
</pre>

修改刚刚设置好的信息  
auto eth0  
iface eth0 inet static  
address 192.168.100.100 (IP)  
gateway 192.168.100.2 (网关)  
netmask 255.255.255.0 (子网掩码)

而后找到需要找到DNS文件

> /etc/resolv.conf

<pre class="lang:sh decode:true " >~$ vi /etc/resolv.conf
</pre>

修改刚刚设置好的信息  
nameserver 192.168.100.2 (网关)

设置好后，保证网络重启。这时候应该虚拟机和主机都能Ping通了。  
这是我们需要使用FTP来连接虚拟机，首先要Linux安装FTP服务

<pre class="lang:sh decode:true " >~$ sudo apt-get install vsftpd
</pre>

此后即可连接FTP了。

可能一直使用Linux的终端比较烦人，所以利用windows下来的Putty更能简化工作，并且更加视觉清晰直观。  
而Putty是需要SSH支持登陆的，所以我们必须在Linux上安装好SSH

<pre class="lang:sh decode:true " >~$ sudo apt-get remove openssh-client
~$ sudo apt-get install openssh-client
~$ sudo apt-get install openssh-server
~$ ps -A | grep ssh
</pre>

安装好后我们就可以使用Putty登陆  
[<img class="alignnone size-full wp-image-740" title="vmware-3" src="{{ site.url }}/uploads/2012/12/vmware-3.jpg" alt="" width="589" height="176" />][4]

 [1]: {{ site.url }}/uploads/2012/12/vmware.jpg
 [2]: {{ site.url }}/uploads/2012/12/vmware-1.jpg
 [3]: {{ site.url }}/uploads/2012/12/vmware-2.jpg
 [4]: {{ site.url }}/uploads/2012/12/vmware-3.jpg