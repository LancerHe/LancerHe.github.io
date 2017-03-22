---
title: Mac brew php7.1环境下安装Runkit
author: 谇雨
layout: post
permalink: /macos-brew-php7-runkit-support.html
categories:
  - Nginx
tags:
  - php7.1
  - Runkit
---

开发机一直使用brew来安装PHP及其他的环境，今天把PHP升到7.1，由于7.1版本下还没有runkit的源，官方也没有更新支持到php7，庆幸Github上有人Fork后做了支持，所以无法使用brew安装，只能编译安装了。

首先下载runkit，解压，进入目录。

只好从 Nginx 上想其他的方案，毕竟之前曾利用 lua 脚本对 MySQL 连接请求进行改造过，果不其然，有类似的模块可以拷贝一个请求。

    git clone https://github.com/runkit7/runkit7
    cd runkit7
    $(brew --prefix homebrew/php/php71)/bin/phpize
    ./configure --with-php-config=$(brew --prefix homebrew/php/php71)/bin/php-config
    make && make install
    make test

`$(brew –prefix homebrew/php/php71)` 即 brew info php71结果中的path值。

由于brew安装PHP会在php.ini同级目录创建conf.d目录，并把扩展的配置文件写在这里，一目了然知道都安装了哪些扩展，所以也以同样方式在此目录创建ext-runkit.ini。

`make install` 后会显示，具体路径可能会不一样。
    
    Installing shared extensions:  /usr/local/Cellar/php71/7.1.0_11/lib/php/extensions/no-debug-non-zts-20160303/

这个目录即扩展.so的存放目录。下边会用到。

    [runkit]
    extension="/usr/local/Cellar/php71/7.1.0_11/lib/php/extensions/no-debug-non-zts-20160303/runkit.so"

至此，重启php-fpm就可以了。