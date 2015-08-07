---
title: 搭建Docker Registry
author: 谇雨
layout: post
permalink: /docker-registry-install.html
categories:
  - Linux
tags:
  - Docker Registry
---

感谢knktc的Docker Registry的搭建方案，成功搭建后并在项目中很好的应用了，转载文章，以下为正文部分。

> 本文转载：[搭建自己的docker registry](http://knktc.com/2014/08/09/docker-registry-install/){:target="_blank"}

平时我们使用的docker镜像都来自官方的docker index，由于国内连接docker官网速度太慢，而且有些包含自己代码的镜像也不好直接放到docker官网上，这个时候就需要考虑自己搭建一个docker index的源了。docker官方提供了docker registry这个工具，用于自建docker index，可在本地网络环境中保存和分享docker image，非常适合于公司内部进行docker的推广和使用。本文记录了安装docker registry的过程，作为一个笔记供大家参考吧。

<!--more-->

### 安装环境

我使用的是Centos 6.5 x64版的系统，docker registry使用python来编写，对于系统环境的要求不会很苛刻。

### 安装过程

#### 使用yum安装依赖

在正式安装前需要安装几个依赖，使用yum就可以完成了：

    yum install python-devel libevent-devel python-setuptools gcc xz-devel

#### 添加docker用户和目录

为了安全起见，我们可以添加一个用户docker，使用这个非root用户来允许docker registry程序，同时指定好docker镜像的存储位置，本处指定为/opt/docker_data/registry目录：

    useradd docker
    mkdir -p /opt/docker_data/registry
    chown -R docker.docker /opt/docker_data/registry

#### 使用easy_install 安装

由于使用python来编写，可以直接使用easy_install(或pip)来安装docker-registry

    easy_install docker-registry

#### 配置docker registry

使用easy_install安装完成的docker-registry位于/usr/lib/python2.6/site-packages/docker_registry-0.7.3-py2.6.egg目录，此时需要进入这个目录下的config子目录，从模板复制一个配置文件出来：

    cd /usr/lib/python2.6/site-packages/docker_registry-0.7.3-py2.6.egg/config
    cp config_sample.yml config.yml

此时可以修改这个config.yml配置文件，需要注意修改以下的两个地方：

    配置sqlite数据库位置
    sqlalchemy_index_database: _env:SQLALCHEMY_INDEX_DATABASE:sqlite://///opt/docker_data/registry/docker-registry.db

    配置本地存储位置
    local: &local
        storage: local
        storage_path: _env:STORAGE_PATH:/opt/docker_data/registry

#### 配置nginx

为了方便使用，可以在本地dns服务器上注册docker.knktc.com这个域名，并使用nginx来进行代理。

配置文件中加入以下语句:

    #for docker
    upstream docker-registry {
        server localhost:5000;
    }

    server {
        listen 80;
        server_name docker.knktc.com;

        proxy_set_header Host       $http_host;   # required for docker client's sake
        proxy_set_header X-Real-IP  $remote_addr; # pass on real client's IP

        client_max_body_size 0; # disable any limits to avoid HTTP 413 for large image uploads

        # required to avoid HTTP 411: see Issue #1486 (https://github.com/dotcloud/docker/issues/1486)
        chunked_transfer_encoding on;
        #     
        location / {
            proxy_pass http://docker-registry;
        }
    }

#### 测试启动

nginx启动之后，使用docker用户执行以下的命令可以测试启动：

    /usr/bin/gunicorn --debug -k gevent -b 0.0.0.0:5000 -w 8 docker_registry.wsgi:application

此时可能会遇到数量众多的import error，逐个使用easy_install 安装缺失的模块就可以了。最后在浏览器中访问http://docker.knktc.com，如果看到以下的输出，则表明docker registry安装成功了：

    "docker-registry server (dev) (v0.7.3)"

#### 使用supervisord来进行进程的监控

官方和其他的文档都推荐使用supervisord来进行docker registry的进程监控，安装supervisor后，在/etc/supervisord中加入以下语句即可：

    [program:docker-registry]
    user=docker
    command=/usr/bin/gunicorn --debug -k gevent -b 0.0.0.0:5000 -w 8 docker_registry.wsgi:application 
    redirect_stderr=true  
    stderr_logfile=/var/log/supervisor/docker-registry-error.log
    stdout_logfile=/var/log/supervisor/docker-registry.log  
    autostart=true  
    autorestart=true

#### 测试使用

docker registry搭建完毕之后就可以使用tag命令将本地的镜像设置为使用本地的docker registry来存储了，一个简单的例子如下：

    #将本地的localimage镜像设置为存储到docker registry上，命名为knktc/testimage
    docker tag localimage docker.knktc.com/knktc/testimage

    #上传这个镜像到docker registry中
    docker push docker.knktc.com/knktc/testimage

    #换一台机器来pull这个镜像
    docker pull docker.knktc.com/knktc/testimage

### 参考链接

[https://github.com/docker/docker-registry](https://github.com/docker/docker-registry){:target="_blank"}

[https://www.dockboard.org/build-your-own-docker-repository/](https://www.dockboard.org/build-your-own-docker-repository/){:target="_blank"}
