---
title: 如何在Centos 6上设置NFS挂载目录
author: 谇雨
layout: post
permalink: /how-to-set-up-an-nfs-mount-on-centos-6.html
categories:
  - Linux
tags:
  - Centos
  - nfs
---

> 本文翻译自：[How To Set Up an NFS Mount on CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6){:target="_blank"}

### 关于 NFS (Network File System) 挂载

NFS挂载工作与多个服务器之间共享一个目录。由于主目录存在一台服务器上，所以有节省磁盘空间的优点，其他的服务器可以连接到该服务上，在设置好挂载后，NFS的永久固定，也是可以被直接访问使用。

### 开始设置

设置NFS至少需要两台服务器，提供共享文件夹的叫做服务端，连接到他的叫做客户端

本教程需要2台服务器：一个是服务端，一个是客户端。首先，我们将建立服务端，然后再建立客户端。以下是他们的IP地址：  
**Master: 12.34.56.789**  
**Client: 12.33.44.555**

该工作必须使用root权限，您可以通过以下方式进入root用户

    sudo su

<!--more-->

### 开始搭建 NFS 服务端
#### 第一步 - 下载依赖库，软件
首先使用 yum 安装 NFS 软件 

    yum install nfs-utils nfs-utils-lib

随后，通过以下脚本启动 NFS 服务

    chkconfig nfs on 
    service rpcbind start
    service nfs start

#### 第二步 - 导出分享的文件夹
下一步是决定我们要与客户端共享的目录。所选择的目录必须添加到 /etc/exports 文件，通过它指定的目录共享位置以及共享详细信息。

假设我们想要分享的目录是 /home

我们需要打开这个文件

    vim /etc/exports

添加以下的信息，分享给客户端

    /home           12.33.44.555(rw,sync,no_root_squash,no_subtree_check)


这些配置项的说明：
+ *rw:* 此选项允许客户端在共享目录中读取和写入 
+ *sync:* 同步文件信息是实时的
+ *no_subtree_check:* 此选项可防止子树检查。当一个共享目录是一个更大的文件系统的子目录，每个目录上面NFS进行扫描，以验证其权限和细节。禁用子树检查可能增加NFS的可靠性，同时降低安全。
+ *no_root_squash:* 允许使用root用户来进入共享文件夹

一旦你设置完成每个共享目录，运行以下命令导出它们：

    exportfs -a

### 开始搭建 NFS 客户端

#### 第一步 - 下载依赖库，软件

首先使用 yum 安装 NFS 软件 

    yum install nfs-utils nfs-utils-lib

#### 第二步 - 挂载目录
当软件安装完成以后，先创建一个目录用于 NFS 挂载

    mkdir -p /mnt/nfs/home

那我们开始挂载上它

    mount 12.34.56.789:/home /mnt/nfs/home

你可以使用 `df -h` 命令来查看是否已经成功挂载，你将会看到以下列表：

    df -h

    Filesystem               Size  Used Avail Use% Mounted on
    /dev/sda                 20G   783M   18G   5% /
    12.34.56.789:/home       20G   785M   18G   5% /mnt/nfs/home

此外，使用 `mount` 命令来查看整个系统的挂载清单

    mount

你将会看到如下列表

    /dev/sda on / type ext4 (rw,errors=remount-ro)
    none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
    sunrpc on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw)
    nfsd on /proc/fs/nfsd type nfsd (rw)
    12.34.56.789:/home on /mnt/nfs/home type nfs (rw,noatime,nolock,bg,nfsvers=2,intr,tcp,actimeo=1800,addr=12.34.56.789)


### 测试你的 NFS 挂载
一旦你已经成功地挂载NFS目录，您可以测试，它通过在客户端上创建文件和检查对服务器的可用性。

在目录中创建一个文件，尝试它：

    touch /mnt/nfs/home/example

你将会在服务端 `/home` 目录下找到这个文件

    ls /home

你可以将挂载的信息写在客户端 `fstab` 文件种，当服务器启动后将会自动的挂载目录。

    vim /etc/fstab
    12.34.56.789:/home  /mnt/nfs/home   nfs      auto,noatime,nolock,bg,nfsvers=3,intr,tcp,actimeo=1800 0 0

你可以通过 `man` 命令得到更多的fstab信息：
    
    man nfs

随后的当任何情况服务器重新启动后，你可以使用这个命令来安装fstab中指定的挂载信息：
    
    mount -a

你将会看到挂载的信息
    
    df -h
    mount

### 卸载挂载目录

如果你需要移除这个挂载目录，将使用 `umount` 命令

    cd
    sudo umount /directory name

然后通过 `mount` 命令可以发现之前挂载的目录已经被卸载：

    df -h

