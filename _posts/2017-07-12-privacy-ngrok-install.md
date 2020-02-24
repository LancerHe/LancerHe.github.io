---
title: Ngrok私有自定义域名部署
author: 谇雨
layout: post
permalink: /privacy-ngrok-install.html
categories:
  - Linux
tags:
  - ngrok
---

Ngrok如果你完全不知道它是什么东西，可以在它的github项目上了解下：https://github.com/inconshreveable/ngrok

简单概括：能够将你本机的HTTP服务或TCP服务，通过部署有ngrok服务的外网伺服器暴露给外网访问，网上所说的内网穿透

之前的几个月里本人一直用官方订阅版本的ngrok，之所以用付费模式，主要因为免费模式每次都生成一个随机的二级域名，让人很不舒服，但实际几个月下来的使用情况，评估发现总体用的频率并不是很高，同时网络效果也不是很好（应该和墙有关），同时也尝试了一下国内小米球Ngrok，同样有一些不稳定因素，所以打算自己编译一个私有的Ngrok，在编译过程中发现了不少问题，重新整理一遍文档。

### 1. 安装go语言开发环境/Git

ngrok是利用go语言开发的，所以先要在服务器上安装go语言开发环境。

```
yum install golang
```

在`~/.bash_profile`文件内，加入以下环境变量配置内容：

```
export GOPATH=$HOME/go
PATH=$PATH:$HOME/.local/bin:$HOME/bin:$GOPATH/bin
```

最后保证可通过go env查看是否配置成功。

安装Git方式略过，这里没有对版本有要求，我们只是利用GIt获取Ngrok源码，不用Git直接Wget github zip文件也是可以的。

### 3. 获取ngrok的源码

由于下面编译过程需要改官方的部分源码，同时这里也测试过官方最新的版本总是存在「remote error: bad certificate」问题（文末有描述）。这里用的是`mamboer`的源码基于1.7.1版本，应该有做一些变更，所以尽可能Fork一份源码至自己的Github账户。

```
cd /usr/local
git clone https://github.com/mamboer/ngrok.git
```

源码拉取下来后，看情况需要修改一个地方：
打开 `src/ngrok/log/logger.go` 文件
将 `code.google.com/p/log4go` 修改为 `github.com/alecthomas/log4go`
这里主要考虑 `code.google.com` 已经下线，其实不修改也可以。

### 4. 生成自签名证书

使用ngrok.com官方服务时，我们使用的是官方的SSL证书。

自建ngrokd服务，证书这里也可以用Godaddy付费证书/Let' Encrypt免费证书，不过出于个人测试或者沙盒的要求，就不用第三方证书。所以我们需要生成自己的自签名证书，并编译一个携带该证书的ngrok客户端。

证书生成过程需要一个「基础域名」。 以ngrok官方随机生成的地址"1iedks2cv.ngrok.com"为例，其「基础域名」就是"ngrok.com"，如果你要 提供服务的地址为”example.ngrok.xxx.com”，那「基础域名」就应该 是”ngrok.xxx.com”。

我们这里以「基础域名」="ngrok.crackedzone.com"为例，因为我将来希望建立"pay.ngrok.crackedzone.com"用于平台支付回调的穿透模式。

在此之前我们给域名"crackedzone.com"添加两条A记录："ngrok.crackedzone.com"和"*.ngrok.crackedzone.com"，指向这台Ngrok所在的服务器ip。

同时在服务器上生成证书的命令如下：

```
cd ngrok
openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=ngrok.crackedzone.com" -days 5000 -out rootCA.pem
openssl genrsa -out device.key 2048
openssl req -new -key device.key -subj "/CN=ngrok.crackedzone.com" -out device.csr
openssl x509 -req -in device.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt -days 5000
```

执行完以上命令，在ngrok目录下就会新生成6个文件：

```
-rw-rw-r-- 1 lv lv  985 Feb 24 19:04 device.crt
-rw-rw-r-- 1 lv lv  895 Feb 24 19:04 device.csr
-rw-rw-r-- 1 lv lv 1679 Feb 24 19:03 device.key
-rw-rw-r-- 1 lv lv 1675 Feb 24 19:01 rootCA.key
-rw-rw-r-- 1 lv lv 1103 Feb 24 19:03 rootCA.pem
-rw-rw-r-- 1 lv lv   17 Feb 24 19:04 rootCA.srl
```

ngrok 编译过程会加载 `assets/client/tls`, `assets/server/tls` 这里的证书资源文件，替换成自己生成的。

```
rm -f assets/client/tls/ngrokroot.crt
rm -f assets/server/tls/snakeoil.crt
rm -f assets/server/tls/snakeoil.key

cp rootCA.pem assets/client/tls/ngrokroot.crt
cp device.crt assets/server/tls/snakeoil.crt
cp device.key assets/server/tls/snakeoil.key
```

### 5. 编译客户端ngrok和服务端ngrokd

```
make release-server release-client
```

成功编译后，会在bin目录下找到ngrokd和ngrok这两个文件。

ngrok可以下载到内网的Client服务器上，存放在`/usr/local/bin/ngrok`

### 6. 服务端运行ngrokd服务

```
nohup /usr/local/ngrok/bin/ngrokd -domain="ngrok.crackedzone.com" -httpAddr=":80" > /tmp/ngrok.log 2>&1 &
```

有需要可以编写一个服务文件，同时记得开放防火墙80端口和4443端，如果证书需要包括443端口。

### 7. 客户端运行ngrok

创建一个配置文件: ngrok.yml

```
server_addr: "ngrok.crackedzone.com:4443"
trust_host_root_certs: false

tunnels:
  pay:
    remote_port: 80
    subdomain: pay
    proto:
      http: 127.0.0.1:10780
```

启动客户端
```
/usr/local/bin/ngrok -config=/root/ngrok.yml start pay
```

### 注意事项
客户端"ngrok.yml"中"server_addr"后的值必须严格与服务端-domain以及证书中的「基础域名」相同，否则Server端就会出现如下错误日志：

```
[02/24/20 09:55:46] [INFO] [tun:15dd7522] New connection from 27.12.33.23:3994
[02/24/20 09:55:46] [DEBG] [tun:15dd7522] Waiting to read message
[02/24/20 09:55:46] [WARN] [tun:15dd7522] Failed to read message: remote error: bad certificate
[02/24/20 09:55:46] [DEBG] [tun:15dd7522] Closing
```

