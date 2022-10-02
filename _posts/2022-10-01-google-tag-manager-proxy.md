---
title: GoogleTagManager 国内代理以及Chrome Extension接入GA4
author: 谇雨
layout: post
permalink: /google-tag-manager-proxy-chrome-extension-ga4.html
categories:
  - Linux
tags:
  - nginx
  - http_sub_module
  - Google Analytics
  - Google Tag Manager
  - Chrome Extension
---

### 背景

Google Analytics(UA) 于2023年7月要求全面下架，所以必须迁移使用 Google Analytics 4(GA4)，在迁移的过程，因为GA4使用的是 GoogleTagManager 的方式进行装载，我们发现在中国内无法加载这个域名。

详细监听请求后，发现 UA 加载的是 `google-analytics.com` 这个域名在国内是可以被请求到，所以整体数据丢失率很低，但是 GA4 显然因为 `www.googletagmanager.com` 被拒绝。

### 方案

既然如此，那么唯一的方案就是找一台海外的服务器，做一个中间代理，反向到 `www.googletagmanager.com` 即可。

我们选择 `Nginx` 中的模块 `http_sub_module`，先重新编译追加模块。

``` shell
# 安装之前需要提取出，当前Nginx编译的模块，然后我们加上 --with-http_sub_module
/usr/local/nginx-1.18.0/sbin/nginx -V

./configure --prefix=/usr/local/nginx-1.18.0 --with-stream --with-stream_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_ssl_module --with-http_gzip_static_module --add-module=/usr/local/src/ngx_devel_kit-0.3.1rc1/ --add-module=/usr/local/src/lua-nginx-module-0.10.13/ --add-module=/usr/local/src/ngx_dynamic_upstream-0.1.6/
make

# 编译完成以防万一 不使用 make install 覆盖，先备份旧版的可执行文件
/etc/init.d/nginx stop
mv /usr/local/nginx-1.18.0/sbin/nginx /usr/local/nginx-1.18.0/sbin/nginx.old.20221001
cp ./objs/nginx /usr/local/nginx-1.18.0/sbin/
/etc/init.d/nginx start
```

当已支持 `http_sub_module` 后，我们只要在需要的配置文件中加上

```shell
location ^~ /gtag/js {
        resolver 8.8.8.8 ipv6=off;
        proxy_set_header Accept-Encoding "";
        sub_filter_types *;
        sub_filter_once off;

        proxy_pass https://www.googletagmanager.com$uri$is_args$args;
        break;
}
```

那么当你访问 `https://www.your-domain.com/gtag/js?G-XXXXXXX`  
将反向代理到 `https://www.googletagmanager.com/gtag/js?G-XXXXXXX`

### 追踪 Chrome Extension

题外话，因为GA4支持多个不同的数据流，当我们在研究 Gtag 装载至 Chrome Extension Popup.html 中时，通过上述方案后 Gtag 确认已经被加载，但迟迟上报任何事件数据。

于是在断点监听 GA4 上报代码中发现存在一段以下代码

```javascript
"http:" != c && "https:" != c && (G(29), e.abort())
```

上述程序的验证 `window.location.protocol` 非 `https` 或 `http` 时就终止逻辑了。由于扩展的协议地址是 `chrome-extension://xxxxxxxxxxxxxxxxxxxxxxx/pupop.html` 所以一直被阻塞不上报数据。

类似文档：[StackOverflow](https://stackoverflow.com/questions/65178568/how-to-disable-protocol-checking-with-gtag-js-for-new-ga4-properties){:target="_blank"}

那么因为我们安装了 `Nginx` 中的模块 `http_sub_module`，该模块其中有一个功能就是将代理返回的文本内容进行替换，那么我们只需要加上如下这句：

```shell
location ^~ /gtag/js {
        resolver 8.8.8.8 ipv6=off;
        sub_filter ';"http:"!=' ';"chrome-extension:"!='; # 把 http 替换为 chrome-extension，毕竟大部分是 https
        proxy_set_header Accept-Encoding "";
        sub_filter_types *;
        sub_filter_once off;

        proxy_pass https://www.googletagmanager.com$uri$is_args$args;
        break;
}
```

此时我们在 Chrome Extension 就能上报数据到 GA4