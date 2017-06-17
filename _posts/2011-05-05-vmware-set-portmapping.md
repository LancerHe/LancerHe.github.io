---
title: Vmware设置端口映射
author: 谇雨
layout: post
permalink: /vmware-set-portmapping.html
views:
  - 567
categories:
  - Linux
tags:
  - VMware
  - 端口映射
---
将应用部署在虚拟机上，如何让其他机器访问上

例如我的主机在局域网中的IP为： 192.168.0.144 

而对于外网的IP为：211.253.124.71

如果在Vmware上使用桥接设置一个IP为192.168.0.143是最容易让局域网的人访问的。但是公司的IP都是被限制的每个人只有一个IP，所以只能通过虚拟机端口映射解决该问题。相当于在IP：192.168.0.144内在建立一个网段。

1、将网络连接模式改为NAT

右击“虚拟机”——>settings&#8230;.——>Network Adapter——> 在network connection 单选框中选择&#8221;NAT“

2、设置端口映射

选择菜单栏”Edit&#8221; ——>“Vitrual Network Editor”——>选择vmnet8——>在vmnet information 中，  
勾选以下三项“NAT（share host&#8217;s IP address&#8230;)”，&#8221;Connet a host virtual adapter&#8230;&#8221;,&#8221;Use local DHCP..&#8221;

在subnet IP:中，输入子网范围，如Subnet IP:192.168.10.0 Subnet mask:255.255.255.0 —

选择右边的&#8221;NAT settings..&#8221;——》在弹出的对话框中,填写以下内容:（根据个自需求）

Gatewap IP:192.168.10.1 

Host port: 8000

type: tcp/udp (默认tcp)

virtual machine IP address:192.168.10.144 

virtual machine port:21 

Description:描述信息

保存退出