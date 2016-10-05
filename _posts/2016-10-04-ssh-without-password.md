---
title: SSH免密码登录的方法
author: 谇雨
layout: post
permalink: /ssh-without-password.md
categories:
  - Linux
tags:
  - ssh
---

由于项目以及服务器管理比较多，OSX上操作直接使用终端，无法如Windows Xshell一般记住密码，每次ssh的时候都要输入密码实在太浪费时间，今天有空一口气全部改为公钥/私钥认证，登录再也不用任何密码了。

实现步骤：

在你的自己的机器下面使用`ssh-keygen`命令来实现创建公钥，使用 `ssh-keygen -t rsa` 来创建密钥，程序会问你存放的目录，如果不需要修改，直接回车两次即可。

将你`~/.ssh`目录中的`id_rsa.pub`这个文件拷贝到你要登录的服务器的`~/.ssh`目录中，然后再运行以下命令来将公钥导入到`~/.ssh/authorized_keys`这个文件中

    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

另外要注意请务必要将服务器上  
`~/.ssh`权限设置为`700`  
`~/.ssh/authorized_keys` 的权限设置为`600`  
这是linux的安全要求，如果权限不对，自动登录将不会生效
完毕之后，退出服务器的登录，再使用ssh登录，你就会发现服务器不会再向你询问密码了.

当然也可以使用`ssh-copy-id`命令: `ssh-copy-id user@server`  

    ssh-copy-id -i 公钥文件路径 user@server
    ssh-copy-id -p 服务器SSH服务端口 user@server