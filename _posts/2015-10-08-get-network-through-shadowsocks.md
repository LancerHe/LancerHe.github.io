---
title: ShadowSocks 科学上网
author: 谇雨
layout: post
permalink: /get-network-through-shadowsocks.html
categories:
  - Linux
tags:
  - ShadowSocks
---

ShadowSocks属于Socks5代理，是一个开源项目。ShadowSocks使用自定义协议，屏蔽和干扰就更为困难，因此相对来说稳定一些。

### Install

有网友做了个服务器端一键安装ShadowSocks的脚本，使用root用户登录，运行以下命令：

    wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-libev.sh
    chmod +x shadowsocks-libev.sh
    ./shadowsocks-libev.sh 2>&1 | tee shadowsocks-libev.log

安装完成后，得到的服务器端口：8383，客户端端口：1080，密码为自己设定的密码。

卸载方法：使用 root 用户登录，运行以下命令：

    ./shadowsocks-libev.sh uninstall

安装完成后即已后台启动 shadowsocks ，运行：

    ps -ef | grep ss-server | grep -v ps | grep -v grep

可以查看进程是否存在。此脚本安装完成后，会将 shadowsocks-libev 加入开机自启动。

使用命令：

    /etc/init.d/shadowsocks start
    /etc/init.d/shadowsocks stop
    /etc/init.d/shadowsocks restart
    /etc/init.d/shadowsocks status

修改端口和加密方式：编辑修改配置文件 

    /etc/shadowsocks-libev/config.json

每隔1小时重启服务：

    crontab -u root -e 输入 0 */1 * * * /etc/init.d/shadowsocks restart

ShadowSocks客户端可以参考这里的(下载地址)[https://shadowsocks.com/client.html]，安装完成后，配置客户端参数。

如果有智能路由器的话，在路由器上安装ShadowSocks，可以不需要在电脑安装客户端。

设置好了后，通过ShadowSocks上网的方案是，设置Shadowsocks为自动启动，其他设置和SSH几乎完全一样，在Dropbox等各类应用通过SOCK5连接，Chrome安装Proxy SwitchySharp或Proxy SwitchyOmega，代理服务器协议为SOCKS5，代理服务器地址127.0.0.1，代理端口1080，选自动切换模式。
