---
title: Mock Server 简单实现
author: 谇雨
layout: post
permalink: /simple-mock-server.html
categories:
  - PHP
  - NodeJS
tags:
  - HTTPServer
  - Mock
---

*利用 Nodejs 创建 HTTP Server*

> 欢迎关注我Github上Mock Server项目：[https://github.com/LancerHe/mock-server](https://github.com/LancerHe/mock-server){:target="_blank"}

### 一．背景

在 Linux 服务端模块测试中，经常会遇到被测模块依赖外部第三方服务，如需要通过Socket 接口调用模块，Curl 请求返回超时，多数时候我们是通过直接连接被调用的模块进行测试，但这并不是一个好方法，原因：

*   被调用模块部署成本很高；
*   数据构成困难；
*   悲观路径测试成本高；
*   性能测试不够好；
*   部分接口根本无法模拟；

对于这种情况，我们需要搭建一个临时的 Server，模拟这些服务，提供数据进行联调
和测试，这就是所谓的 Mock Server。

<!--more-->

### 二．Mock Server 的业务设计

因此我们将设计一个最简单的 Mock Server，只支持 HTTP 方式请求，但是满足一些基本测试需求。

场景 1：业务方通过 HTTP 请求后端接口服务，并返回需要的 JSON 字符串，如：  
请求：GET http://demo.site.com/api/resource/id/2  
返回：{"id":2}

场景 2：业务方通过 HTTP 请求后端接口服务，服务器超时，延迟 10 秒才返回；

场景 3：业务方通过 HTTP 请求后端接口服务，需要返回对应的状态码，比如 404 not found 或者 500 错误等；

场景 4：业务方通过 HTTP 请求后端接口服务，返回的业务方请求的部分数据，如通POST 参数 name=tom，希望在 Response 时也能出现这个值；

场景 5：能够灵活的通过 JSON 文件来配置所有 Mock 结果，如：

    request: {
        query : {
            id: '1', name: '2'
        }
    }
    , response: {
        , body: {
            user: 'LancerHe',
            pass: '12121212',
            remember : 1,
        }
    }

Query 为请求 GET 参数，Response 中的结果为输出的 JSON 字符串。

### 三．Mock Server 的程序设计

以上这些场景足以覆盖一定量的测试需求，但这个 Mock Server 似乎也不简单，由于延迟性需求，配置的 JSON 化以及将来涉及性能测试相关，决定使用 NodeJS 作为服务端 Mock Server 的编程语言，并使用 PHP 语言做将来的界面配置管理。

*创建一个 HttpServer*

    var url  = require('url'),
        http = require('http'),
        fs = require('fs'),
        querystring = require('querystring');

    httpserver = http.createServer(function(request, response) {
        request.setEncoding('utf-8');
        urlinfo  = url.parse(request.url);
        query = querystring.parse(urlinfo.query);
        var post = '';
        request.addListener("data", function(data) {
            post += data;
        });

        request.addListener("end", function() {
            post = querystring.parse(post);
            response.write("{'a':'b'}");
            response.end();
        });
    });
    httpserver.listen('8096', "0.0.0.0");

对于 NodeJS 创建一个 Http 服务器并绑定一个监听脚本是非常容易的事，同时通过 url, querystring 库可以快速的将请求的 get，post 转化成 json 结构，当 http 接受完成后可以通过 Response 对象进行 http reponse，此时通过 http 方式访问 http://192.168.156.124:8096/ 将会看到结果。

*通过 http 请求路由加载对应的 JSON 配置*

配置需要使用 exports 方式将配置的 JSON 配置对外被使用  
做一个简单的规范  
若请求：http://192.168.156.124:8095/api/resource/info  
对应的 JSON 配置文件：/mockserver/config/api/resource/info.js  
通过 require 加载 config 即可  

    urlinfo  = url.parse(request.url);
    mockfile = require('path').resolve() + "/config" + urlinfo.pathname + '.js';

    if ( ! fs.existsSync(mockfile) ) {
        response.write("Mock config file not exists!");
        response.end();
        return false;
    }
    config = require(mockfile).mock;

*在配置 Response 中使用 Resquest 的信息* 

如将 post 的 user 信息直接 302 重定向到指定的 Webserver 上：

    exports.mock = [
        {
            request: {
            }
            , response: {
                statusCode: 302
                , header: {
                    "Location": "http://xxx.site.com/?user={$request.post.user}"
                }
                , body: {

                }
            }
        }
    ]

针对这一特殊情况，我们针对{$request.query.xxxx}以及{$request.post.xxxx}做替换即可，只要做一个简单的替换函数：

    if ( mock.response.header ) {
        for( key in mock.response.header ) {
            response.setHeader( key, replaceRequest(httprequest, mock.response.header[key] ) );
        }
    }

    function replaceRequest(httprequest, string) {
        if ( 'string' != typeof string) {
            return string;
        }

        var regex   = /{\$[a-zA-Z1-9_.]*}/g;
        var matches = string.match(regex);
        for (m in matches) {
            var keyword = matches[m];
            var match   = keyword.replace("{$", "").replace("}", "").split('.');
            if ( match[0] != 'request' ) {
                continue;
            }

            if ( match[1] == 'query' ) {
                if ( undefined != typeof httprequest.query[match[2]] ) {
                    string = string.replace(keyword, httprequest.query[match[2]]);
                }
            }

            if ( match[1] == 'post' ) {
                if ( undefined != typeof httprequest.post[match[2]] ) {
                    string = string.replace(keyword, httprequest.post[match[2]]);
                }
            }
        }
        return string;
    }

*服务端延迟输出与渲染状态码*

利用setTimeout方法：

    var timeout = mock.response.delay ? mock.response.delay : 1;

    response.setTimeout(timeout, function() {
        if ( mock.response.statusCode ) {
            response.statusCode = mock.response.statusCode;
        }
        response.write( JSON.stringify( mock.response.body ) );
    }

两个方法都比较简单，作用往磁盘中按照一定规则输出一行Log，因此我在一个控制器中来测试它，通过Web访问或者CLI的方式请求：

### 四．Mock Server 适用场景

1.   我开发的模块已经完成，但是依赖模块（server 端）还未完成，接口明确的情况下，可以先 mock server 端接口，调试代码。
2.   我测试的系统依赖第三方服务 Passport，UC 接口，测试阶段不好连接真实 UC 接口，可以 Mock UC 接口，进行测试。
3.   我测试的系统依赖联调接口，连接真实接口，无法构造测试场景（如异常测试），可以 mock 对方接口，设定接口响应分支，进行测试。
4.   我测试的系统依赖外部接口，自动化测试比较困难，可以 Mock 外部接口，根据测试 case，设定响应，实现自动化。

### 五．意义

通过 MockServer 平台配置，支持 http 接口协议，简单配置 JSON，可以多个产品线共享使用。

1.   减少测试工作量，提高测试效率，降低测试成本；
2.   支持自动化测试与持续集成；
3.   提高 RD 开发效率，不在为等待联调接口而烦恼；
