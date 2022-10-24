---
title: Nginx 自动禁止爬虫IP采集
author: 谇雨
layout: post
permalink: /nginx-block-ip-crawler.html
categories:
  - Linux
tags:
  - nginx
  - ngx_http_limit_req_module
  - 防爬虫
---

### 背景

最近我们有一个公开服务提供给客户查询关键词的热度值，由于这个API做在官方网站上，自然没有用户登陆，也没有很高查询成本，所以设计上没有任何鉴权无法进行身份认定，于是就被一个爬虫开了超高并发请求，直接后端的AWS Tomcat CPU被用尽，导致无法响应。

爬虫显然是买了动态代理IP池，存在大量的不同IP进行请求，所以我们仅仅将几个IP加入 `deny` 策略是无法对其阻止。

### 方案

只好先用上 Nginx 自带的限制并发的模块 `ngx_http_limit_req_module` 做两个限制池，一次IP每秒3次请求，每分钟20个请求

```
http {
    limit_req_zone $binary_remote_addr zone=search_second:10m rate=3r/s;
    limit_req_zone $binary_remote_addr zone=search_minute:10m rate=20r/m;
}
```

在需要的 location 附加这块的限制

```
location ^~ /search{
        limit_req zone=search_second;
        limit_req zone=search_minute;
}
```

此时 Nginx error log 出现了大量的503请求，此时我们发现我们的CPU额度慢慢回复了，那么我们还需要一个优化，出现一定次数的503 IP，我们就可以标记为它为爬虫IP，自动追加到 block 列表中。

block-ip.sh

```
#!/bin/bash
ERR_LOG=/usr/local/nginx/logs/error.log
BLOCK_IP_FILE=/usr/local/nginx/conf/blockips.conf
BLOCKED_IP=/usr/local/nginx/conf/blocked-ip.txt
BLOCK_IP=/usr/local/nginx/conf/block-ip.txt
NGINX_CMD=/usr/local/nginx/sbin/nginx

#把屏蔽列表备份到blocked-ip.txt文件
#从错误日志中提取恶意访问IP记录到block-ip.txt文件
#清空错误日志
#重启Nginx

cat $BLOCK_IP_FILE > $BLOCKED_IP &&
/bin/sed -nr 's#.*[^0-9](([0-9]+\.){3}[0-9]+).*#\1#p' $ERR_LOG |/bin/awk '{IP[$1]++}END{for (i in IP) print IP[i],i}'|/bin/awk '{if($1>20)print "deny "$2";"}' > $BLOCK_IP &&
/bin/grep -v -f $BLOCK_IP_FILE $BLOCK_IP >> $BLOCK_IP_FILE &&
#cat /dev/null > $ERR_LOG &&
$($NGINX_CMD -s reload)
```

添加进入计划任务5分钟一次 crontab -e

```
*/5 * * * * /bin/bash /usr/bin/block-ip.sh &> /dev/null
```

类似文档：[Runbing's Blog](https://runbing.cc/archives/nginx-limite-request-and-block-ip.html){:target="_blank"}

此时虽然负载下降，服务资源也可用，但是实际上对爬虫采集没有任何威胁，只是拉长它采集的时间而已，还是对我们的网站念念不忘，于是灵机一动，决定给他返回状态码从503改成正常返回200，同时构造了一个空返回值，毕竟很多关键词返回都是空的，这样我们就混淆的所有的结果，让爬虫获得非正确的结果数据。

我们在 `search` 设置一个 503 地址转 200 到一个固定的 Uri 上，通过这个 Uri 直接 Response API.

```
location ^~ /search{
        limit_req zone=search_second;
        limit_req zone=search_minute;
        error_page 503 =200 /search-term-503-res;
}

location /search-term-503-res {
        return 200 '{"code":1,"data":[]}';
}
```

当我们频繁刷新就会发现前几次数据正常，后面都都返回空数据，过一会又正常了，当然对于正常客户不会有类似的情况。