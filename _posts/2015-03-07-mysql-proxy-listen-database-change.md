---
title: MySQL Proxy监听数据库表结构变更
author: 谇雨
layout: post
permalink: /mysql-proxy-listen-database-change.html
categories:
  - PHP
  - NodeJS
tags:
  - MySQL Proxy
  - 监听
  - 表结构更变
---

最近团队在进行持续集成时发现，RD团队快速开发的过程中对数据表结构更变的频率非常高，当提测QA团队出现一个致命问题，当前 TAG 数据表比前一个 TAG 的差异性在哪里？

其实不能全怪RD们，在紧张的开发过程中优先都以交付项目为目标，根本无暇顾及这些变更细节，但是做持续集成就必须知道这个版本的差异在哪里，同时快速的进行部署和测试。其本质的目标是保证和产品质量无差异。

根据目前的情况，RD开发机连接的是RD开发数据库服务器3306端口进行通信，同时RD们如果使用客户端工具在办公网进行连接也是走3306端口，那么为什么不能把 3306 作为一个代理的端口，将真实的连接请求转向后端使用MySQL服务，这样是否就能通过转发知道所有的SQL语句，那么匹配出我们需要的 SQL 或许就能解决目前的问题。

流量拓扑图：

![流量拓扑图]({{ site.url }}/uploads/2015/03/mysql-proxy.jpg)

<!--more-->

> MySQL Proxy就是这么一个中间层代理，简单的说，MySQL Proxy就是一个连接池，负责将前台应用的连接请求转发给后台的数据库，并且通过使用lua脚本，可以实现复杂的连接控制和过滤，从而实现读写分离和负载平衡。

对于应用来说，MySQL Proxy是完全透明的，应用则只需要连接到 MySQL Proxy 的监听端口即可。

### 安装 MySQL Proxy

#### Lua 依赖 GLIB

    cd /usr/local/src/
    wget "http://ftp.gnome.org/pub/gnome/sources/glib/2.18/glib-2.18.4.tar.gz"
    tar xvf glib-2.18.4.tar.gz
    cd glib-2.18.4
    ./configure
    make && make install
    cd ..
    rm -rf glib-2.18.4

#### Lua 依赖 readline

    cd /usr/local/src/
    wget "ftp://ftp.cwru.edu/pub/bash/readline-6.1.tar.gz"
    tar xvf readline-6.1.tar.gz
    cd readline-6.1
    ./configure
    make && make install
    cd ..
    rm -rf readline-6.1

#### Lua

    ldconfig -v
    cd /usr/local/src/
    wget "http://www.lua.org/ftp/lua-5.1.4.tar.gz"
    tar xvf lua-5.1.4.tar.gz
    cd lua-5.1.4
    sed -i '11s/^.*$/CFLAGS= -O2 -Wall -fPIC \$(MYCFLAGS)/g' src/Makefile
    make linux  
    make install
    cp etc/lua.pc /usr/local/lib/pkgconfig/   
    export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig 
    cd ..
    rm -rf lua-5.1.4

#### MySQL Proxy请移步官方下载

    tar xvf mysql-proxy-0.8.5.tar.gz
    cd mysql-proxy-0.8.5
    ./configure --prefix=/usr/local/mysql-proxy-0.8.5
    make && make install
    cp lib/rw-splitting.lua /usr/local/lib/
    cp lib/admin.lua /usr/local/lib/
    cd ..
    rm -rf mysql-proxy-0.8.5

#### 配置 MySQL-Proxy转发
    
    # 先创建一个LUA脚本 后面在编写，优先保证服务安装和启动时正常的
    touch /data/change.lua
    echo "
    [mysql-proxy]
    daemon=false
    proxy-backend-addresses=127.0.0.1:3300
    proxy-address=0.0.0.0:3306
    proxy-lua-script=/data/change.lua
    log-file=/var/log/mysql-proxy.log
    log-level=debug
    keepalive=true
    " > /etc/mysql-proxy.cnf
    chmod 0660 /etc/mysql-proxy.cnf

    # 启动服务
    /usr/local/mysql-proxy-0.8.5/bin/mysql-proxy --defaults-file=/etc/mysql-proxy.cnf

服务启动后意味着我们后端的 MySQL 是 3300，MySQL Proxy 占用了 3306 端口进行转发，在转发时可以通过 /data/change.lua 进行自定义转发，与网上很多读写分离原理类似。

### Lua监听数据库表结构变更脚本

MySQL Proxy手册：[http://dev.mysql.com/doc/mysql-proxy/en/](http://dev.mysql.com/doc/mysql-proxy/en/){:target="_blank"}  
MySQL Proxy对象关系图（这个对编写代码很关键）：[http://dev.mysql.com/doc/mysql-proxy/en/mysql-proxy-scripting.html](http://dev.mysql.com/doc/mysql-proxy/en/mysql-proxy-scripting.html){:target="_blank"}

read_query 是在 Proxy 之前执行。

    function read_query( packet )
            -- 判断是QUERY
            if packet:byte() == proxy.COM_QUERY then
                    -- 判断是ALTER CREATE DROP前缀
                    if is_alter(packet) or is_create(packet) or is_drop(packet) then
                            -- 加入转发队列
                            proxy.queries:append(1, packet)
                            -- 加入一条新的 QUERY : 连接 notify 表
                            proxy.queries:append(2, string.char(proxy.COM_INIT_DB) .. 'notify', {resultset_is_needed = true} )
                            -- 加入一条新的 QUERY : 插入 notify 表当前的变更SQL信息，数据库，时间等
                            proxy.queries:append(2, string.char(proxy.COM_QUERY) .. "INSERT INTO `notify` (`db`, `query`, `datetime`) VALUES('".. get_dbname(packet) .."','".. get_query(packet) .."', '" .. get_datetime() .. "')", {resultset_is_needed = true} )
                            -- 加入一台新的 QUERY : 连接回原来的数据库
                            proxy.queries:append(2, string.char(proxy.COM_INIT_DB) .. get_dbname(packet), {resultset_is_needed = true} )

                            return proxy.PROXY_SEND_QUERY
                    end
            end
    end

read_query_result 是 Proxy 后得到的结果

    function read_query_result(inj)
            -- 如果id是2 也就是属于自己额外连接使用notify的三条数据，不用特殊处理了。
            if inj.id == 2 then
                    return proxy.PROXY_IGNORE_RESULT
            end

            -- 否则就写下日志
            write_log(inj)
    end

以下是一些辅助函数

    function get_datetime()
            return os.date("%Y-%m-%d %H:%M:%S", os.time())
    end

    function is_create(query)
            return "CREATE" == string.upper(query:sub(2, 7));
    end

    function is_drop(query)
            return "DROP" == string.upper(query:sub(2, 5));
    end

    function is_alter(query) 
            return "ALTER" == string.upper(query:sub(2, 6));
    end

    function get_dbname(query)
            if type( string.match(query, "`(%w+)`%.") ) == 'string' then
                    return string.match(query, "`(%w+)`%.")
            else
                    return proxy.connection.client.default_db
            end
    end

    function get_query(query)
            return string.gsub(query:sub(2), "'", '"')
    end

    function build_notify(inj)
            return "Time: \n\t" .. get_datetime() .. "\nDatabase: \n\t" .. get_dbname(inj.query) .. "\nQuery: \n\t" .. get_query(inj.query) .. "\n"
    end

    function write_log(inj)
            local file = io.open("/var/log/mysql-proxy-alter", "a")
            file:write(build_notify(inj) .. "\n")
            file:close()
    end