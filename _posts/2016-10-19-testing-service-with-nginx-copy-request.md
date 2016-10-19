---
title: 通过Nginx拷贝请求流量到测试环境测试
author: 谇雨
layout: post
permalink: /testing-service-with-nginx-copy-request.html
categories:
  - Nginx
tags:
  - 拷贝流量
  - 引流测试
---

最近通过 Java 重写了一个简单但流量较大的 PHP 项目，但需要做一个稳定的线上测试，将 PHP 项目的流量拷贝一份至 Java 项目上，在保证项目稳定的同时，从而评估出 Java 服务在性能和成本上的变更。

于是我最快联想到的解决方案是 tcp copy，在生产环境上部署和实施后，发现流量无法拷贝到测试服务器上，排查一天也没有结果，可能与我使用云服务提供商 AWS 有关。

只好从 Nginx 上想其他的方案，毕竟之前曾利用 lua 脚本对 MySQL 连接请求进行改造过，果不其然，有类似的模块可以拷贝一个请求。

                        |-> Nginx -> PHP                    |--> Nginx -> Java
                        |     |------Copy------|            |
    Http Request -> ELB |-> Nginx -> PHP       +----> ELB --|--> Nginx -> Java
                        |     |------Copy------|            |
                        |-> Nginx -> PHP       |            |--> Nginx -> Java
                        |     |------Copy------|

<!--more-->

需要 Nginx 支持部署以下三个模块：

* [headers-more-nginx-module](https://github.com/openresty/headers-more-nginx-module){:target="_blank"}
* [lua-nginx-module](https://github.com/openresty/lua-nginx-module){:target="_blank"}
* [ngx_devel_kit](https://github.com/simpl/ngx_devel_kit){:target="_blank"}  

还需要 Lua 的支持，推荐使用 LuaJIT-2.0.0

这里做了自动化编译的脚本：

    cd /usr/local/src/
    wget http://nginx.org/download/nginx-1.4.4.tar.gz
    wget https://github.com/openresty/headers-more-nginx-module/archive/v0.31.tar.gz
    mv v0.31.tar.gz headers-more-nginx-module-v0.31.tar.gz

    wget https://github.com/openresty/lua-nginx-module/archive/v0.9.13.tar.gz
    mv v0.9.13.tar.gz lua-nginx-module-v0.9.13.tar.gz

    wget https://github.com/simpl/ngx_devel_kit/archive/v0.3.0.tar.gz
    mv v0.3.0.tar.gz ngx_devel_kit-v0.3.0.tar.gz

    wget http://luajit.org/download/LuaJIT-2.0.0-beta9.tar.gz
    tar zxvf LuaJIT-2.0.0-beta9.tar.gz

    cd LuaJIT-2.0.0-beta9
    make 
    make install PREFIX=/usr/local/luajit
    ln -sf luajit-2.0.0-beta9 /usr/local/luajit/bin/luajit

    export LUAJIT_LIB=/usr/local/luajit/lib
    export LUAJIT_INC=/usr/local/luajit/include/luajit-2.0

    cd /usr/local/src/
    tar zxvf lua-nginx-module-v0.9.13.tar.gz 
    tar zxvf headers-more-nginx-module-v0.31.tar.gz 
    tar zxvf ngx_devel_kit-v0.3.0.tar.gz 
    tar zxvf nginx-1.4.4.tar.gz 
    cd nginx-1.4.4

    ./configure --prefix=/usr/local/nginx-1.4.4 --with-http_stub_status_module --with-http_ssl_module --with-http_gzip_static_module --with-ld-opt="-Wl,-rpath,$LUAJIT_LIB" --add-module=/usr/local/src/ngx_devel_kit-0.3.0/ --add-module=/usr/local/src/lua-nginx-module-0.9.13/ --add-module=/usr/local/src/headers-more-nginx-module-0.31/
    make
    make install
    /usr/local/nginx-1.4.4/sbin/nginx -V

编写一个 copy request 的 lua 脚本：  
/usr/local/nginx-1.4.4/conf/lua/copy_req.lua

    local res1, res2, action
    action = ngx.var.request_method
    if action == "POST" then
            arry = {method = ngx.HTTP_POST, body = ngx.req.read_body()}
    else
            arry = {method = ngx.HTTP_GET}
    end

    if ngx.var.svr == "on" then
            res1, res2 = ngx.location.capture_multi {
                    { "/product" .. ngx.var.request_uri , arry},
                    { "/test" .. ngx.var.request_uri , arry},
            }
    else
            res1, res2 = ngx.location.capture_multi {
                    { "/product" .. ngx.var.request_uri , arry},
            }
    end

    if res1.status == ngx.HTTP_OK then
            local header_list = {"Content-Length", "Content-Type", "Content-Encoding", "Accept-Ranges"}
            for _, i in ipairs(header_list) do
                    if res1.header[i] then
                            ngx.header[i] = res1.header[i]
                    end
            end
            ngx.say(res1.body)
    else
            ngx.status = ngx.HTTP_NOT_FOUND
    end

大概的思路为通过 ngx.location.capture_multi 往 /product 与 /test 路由都发一份请求，那么我们 Nginx 配置文件中就需要对 /product, /test 路由做一个重写，指向我们对正式/测试对 upstream

生产环境的 Nginx vhost 文件：  
/usr/local/nginx-1.4.4/conf/vhosts/api.crackedzone.com.conf

    upstream product {
            server  127.0.0.1:8080;
    }
    upstream test {
            server  api-java.crackedzone.com:80;
    }
    server {
            listen       80;
            #lua_code_cache off;

            location / {
                root   html;
                index  index.html index.htm;
            }
            location ~* ^/product {
                    log_subrequest on;
                    rewrite ^/product(.*)$ $1 break;
                    proxy_set_header Host $host;
                    proxy_set_header X-Real-IP $remote_addr;
                    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_pass http://product;
                    #access_log logs/product-upstream.log main;
            }

            location ~* ^/test {
                    log_subrequest on;
                    rewrite ^/test(.*)$ $1 break;
                    proxy_set_header Host $host;
                    proxy_set_header X-Real-IP $remote_addr;
                    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                    proxy_pass http://test;
                    #access_log logs/test-upstream.log main;
            }

            location ~* ^/(.*)$ {
                    client_body_buffer_size 2m;
                    set $svr     "on";               #开启或关闭copy功能
                    content_by_lua_file    "conf/lua/copy_req.lua";
            }
    }

    server {
            listen      8080;
            server_name api.crackedzone.com;
            index       index.html index.htm index.php;
            root        /home/html/api.crackedzone.com/public;

            client_max_body_size     10m;
            client_body_buffer_size  5m;
            fastcgi_buffer_size      128k;
            fastcgi_buffers          8 128k;
            fastcgi_connect_timeout  30;
            fastcgi_send_timeout     30;
            fastcgi_read_timeout     30;

            if ($host !~ "^((.*\.)?(crackedzone\.com)|localhost|\d{1,3}(\.\d{1,3}){3})(:\d+)?$") {
                    return 403;
            }

            location ~* /(\.svn|\.git){
            location ~* /(\.svn|\.git){
                    deny all;
            }

            location ~* /\.(sql|bak|inc|old)$ {
                    deny all;
            }

            if ( !-e $request_filename ) {
                    rewrite ^(.*)$ /index.php?r=$1 last;
                    break;
            }

            location ~ \.php$ {
                    fastcgi_pass  127.0.0.1:9000;
                    fastcgi_index index.php;
                    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                    include       fastcgi_params;
            }

            access_log  /var/log/nginx/api.crackedzone.com-access.log main;
            error_log   /var/log/nginx/api.crackedzone.com-error.log;
    }

测试环境的 Java Spring Boot 指定启动端口为 8080

Nginx vhost 文件： /usr/local/nginx-1.4.4/conf/vhosts/api.crackedzone.com.conf

    server {
            listen      80;
            #server_name api.crackedzone.com api-java.crackedzone.com;

            client_max_body_size     10m;
            client_body_buffer_size  5m;

            if ($host !~ "^((.*\.)?(crackedzone\.com)|localhost|\d{1,3}(\.\d{1,3}){3})(:\d+)?$") {
                    return 403;
            }

            location ~* /(\.svn|\.git){
                    deny all;
            }

            location ~* /\.(sql|bak|inc|old)$ {
                    deny all;
            }

            location / {
                    proxy_pass                      http://127.0.0.1:8080;
                    proxy_set_header                Content-Type 'text/html; charset=utf-8';
                    proxy_pass_request_headers      on;
            }

            access_log  /var/log/nginx/api.crackedzone.com-access.log main;
            error_log   /var/log/nginx/api.crackedzone.com-error.log;
    }

生产环境重启Nginx后，可以同时监听生产环境与测试环境 access_log, 就可以见到同时都在追加中。