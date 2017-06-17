---
title: Linux Git安装
author: 谇雨
layout: post
permalink: /linux-git-install.html
categories:
  - Linux
tags:
  - git
  - Linux
  - 安装
---
相对于比较早期的系统，如Centos 5版本其实默认是没有安装Git。  
所以只能手动安装

[安装包下载](http://code.google.com/p/git-core/downloads/list){:target="_blank"}

可以选择一个自己认为的稳定安装源，这里我选择git-1.8.3.1

    wget http://git-core.googlecode.com/files/git-1.8.3.1.tar.gz
    tar -xvf git-1.8.3.1.tar.gz
    cd git-1.8.3.1/
    ./configure
    make && make install

git依赖`zlib-devel`，`openssl-devel`，`perl`，`cpio，`expat-devel`，gettext-devel``这些包，  
如果configure报错，通过yum安装缺失的依赖包即可。

安装完成后，可以通过查看版本来测试是否安装成功。

    git --version