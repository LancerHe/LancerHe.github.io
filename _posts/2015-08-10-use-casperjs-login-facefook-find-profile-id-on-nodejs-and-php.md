---
title: 在PHP与NodeJS上通过CasperJs登录facebook获取profile id
author: 谇雨
layout: post
permalink: /use-casperjs-login-facefook-find-profile-id-on-nodejs-and-php.html
categories:
  - PHP
  - NodeJS
  - CasperJS
tags:
  - CasperJs
  - facebook
---

神奇的CasperJs

最近的这个业务真心让我感觉有点蛋疼，由于客户端毫无下限的行为，服务端为了配合其数据结构，需要拿到facebook profile id(uid)更好的与客户端做对接，完成Web版业务功能。像facebook这样的大网站，只会给每一个接入的application分配一个appid与其uid建立用户映射关系，就像接入腾讯的应用不会告诉你QQ号一样，因为这已经慢慢涉及他的核心业务，如果暴露给你，意味着你可以轻易的获取好友信息，个人信息等。

根据接入application，由于或者到用户的必要授权，其实是可以获取到一些用户的信息，比如用户主页，用户基本信息等，但这些在后端服务去访问的时候要求用户是登录状态（也就是存在cookie），一开始使用curl拷贝cookie的方式进行尝试，发现是能够获取到用户主页的信息，意味着实际上能够通过正则分解出需要的profile id，那么问题就来了，这个cookie需要登录才能出现。

<!--more-->

那么第一步想到的就是再次利用curl来进行post登录，在Chrome上利用开发者工具Copy as Curl得到：

    curl -v 'https://www.facebook.com/login.php?login_attempt=1' -H 'ra-sid: 6E5A7771-20150731-032221-9bf42a-668b44' -H 'origin: https://www.facebook.com' -H 'accept-encoding: gzip, deflate' -H 'accept-language: zh-CN,zh;q=0.8,en;q=0.6,es;q=0.4,ja;q=0.2,ko;q=0.2,zh-TW;q=0.2' -H 'cookie: datr=dR27Vfzc7TLhENprdMqSj__L; locale=en_US; fr=0ObmZXRx3PymfVL7O.AWUb-QeALpXaWWYl_VMAJj00OJ8.BVux2A.xc.FXF.0.AWVp7rFw; lu=TAYSa-rs0yzT7QE2QwhkR9xg; _js_reg_fb_gate=https%3A%2F%2Fwww.facebook.com%2F%3Fstype%3Dlo%26jlou%3DAfcYfwvE0XKhWtkBOCwGfNFea0cR1DQbiUdj2e-fGVKx7ZYTPeAa6XFDctiOiHsW-nSgbdc0weT5ovs5x52C6RACUjexLKnpoJ7jqTrQqAZ4Pw%26smuh%3D20533%26lh%3DAc8jWhUSLqfC0yFZ; reg_fb_ref=https%3A%2F%2Fwww.facebook.com%2F%3Fstype%3Dlo%26jlou%3DAfcYfwvE0XKhWtkBOCwGfNFea0cR1DQbiUdj2e-fGVKx7ZYTPeAa6XFDctiOiHsW-nSgbdc0weT5ovs5x52C6RACUjexLKnpoJ7jqTrQqAZ4Pw%26smuh%3D20533%26lh%3DAc8jWhUSLqfC0yFZ; reg_fb_gate=https%3A%2F%2Fwww.facebook.com%2F%3Fstype%3Dlo%26jlou%3DAfcYfwvE0XKhWtkBOCwGfNFea0cR1DQbiUdj2e-fGVKx7ZYTPeAa6XFDctiOiHsW-nSgbdc0weT5ovs5x52C6RACUjexLKnpoJ7jqTrQqAZ4Pw%26smuh%3D20533%26lh%3DAc8jWhUSLqfC0yFZ; _js_datr=dR27Vfzc7TLhENprdMqSj__L; reg_ext_ref=; _js_reg_fb_ref=https%3A%2F%2Fwww.facebook.com%2Flogin.php; act=1439218382333%2F0' -H 'upgrade-insecure-requests: 1' -H 'user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/44.0.2403.130 Safari/537.36' -H 'content-type: application/x-www-form-urlencoded' -H 'accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'cache-control: max-age=0' -H 'referer: https://www.facebook.com/login.php' -H 'ra-ver: 3.0.7' --data 'lsd=AVpVRquA&display=&enable_profile_selector=&legacy_return=1&profile_selector_ids=&trynum=1&timezone=-480&lgndim=eyJ3IjoxMjgwLCJoIjo4MDAsImF3IjoxMjgwLCJhaCI6NzMxLCJjIjoyNH0%3D&lgnrnd=075256_4zgF&lgnjs=1439218378&email=lancer.he%40gmail.com&pass=xxxxxxxxxx&persistent=1&default_persistent=1' --compressed

直接拷贝到命令行，注意到Resposne Header中存在：
    
    < P3P: CP="Facebook does not have a P3P policy. Learn why here: http://fb.me/p3p"

同时大部分情况是没有Response Body，Facebook应该早有防范，猜想在访问登录页面时，需要请求一些服务在页面生成了一些cookie或者隐藏的input必要的数值，并且有随机因子成分。

那么这么一来问题就更棘手了，我们总不能用一个手机客户端在那边定时登录来获得cookie验证信息吧，这个想法确实有点Low，不过引发了思考，之前有想法使用 `PhantomJS` 做网页图片的想法，因为 `PhantomJS` 是有 `webkit` 引擎的，意味着实际上可以在服务端上打开一个模拟的浏览器，通过这个浏览器就可以实现facebook的登录机制，那么获取profile id就轻而易举。

`PhantomJS` 则是一个基于 `webkit` 内核，能够运行JS调用API接口的工具，查阅了相关的资料，发现 `CasperJS` 是基于 `PhantomJS` 基础上的拓展工具，更加适合开发者进行开发工作。

因此需要先部署 `PhantomJS(version 1.8+)` 与 `CasperJS`：

Mac下相对方便：
    
    brew install phantomjs
    brew install casperjs

Linux下可以参考

[PhantomJS Document](http://phantomjs.org/build.html){:target="_blank"}, 
[CasperJS Document](http://docs.casperjs.org/en/latest/installation.html){:target="_blank"}
    
    yum -y install gcc gcc-c++ make flex bison gperf ruby openssl-devel freetype-devel fontconfig-devel libicu-devel sqlite-devel libpng-devel libjpeg-devel
    git clone git://github.com/ariya/phantomjs.git
    cd phantomjs
    git checkout 2.0
    ./build.sh

    git clone git://github.com/n1k0/casperjs.git
    cd casperjs
    ln -sf `pwd`/bin/casperjs /usr/local/bin/casperjs

### 通过PHP进行Facebook登录

有了这两个工具，在Gtihub上可以找到 phpcasperjs 库，那么在PHP下就可以通过composer进行安装：

    {
        "require": {
            "phpcasperjs/phpcasperjs": "dev-master"
        },
        "minimum-stability": "stable"
    }

然后就像你亲自登录facebook一般，访问登录页，填写表单，登录成功以后，抓取你需要的页面：

    $username = '<your-username>';
    $password = '<your-password>';
    $casper = new Browser\Casper();
    // for exemple to ignore ssl errors
    $casper->setOptions(array(
        'ignore-ssl-errors' => 'yes'
    ));

    // navigate to google web page
    $casper->start('https://www.facebook.com/login.php');
    $casper->fillForm('form#login_form', array(
        'email' => $username,
        'pass'  => $password
    ), true);
    $casper->thenOpen("https://www.facebook.com/lancer.he.5");
    $casper->run();

    // var_dump($casper->getRequestedUrls());

    $content = ''; $start = 0; $close = 0;
    foreach ($casper->getOutput() as $output) {
        if ( strpos($output, "[CURRENT_PAGE_CONTENT]") !== false) {
            $start = 1;
        } elseif ( strpos($output, "[CURRENT_HTML]") !== false ) {
            $close = 1;
        }
        if ( $start == 1 && $close == 0 ) {
            $content .= $output;
        }
    }
    echo ($content);

当然通过PHP的效率相对来不高，不如做一个简单的服务，使用nodejs http模块来完成。

### 通过NodeJS进行facebook登录

nodejs 目前使用 casperjs 库没有成功访问页面，但是网上推荐使用 spooky 来驱动 casperjs

    npm install spooky

使用 spooky 代码上与使用原生 casperjs 没太大差异：

    var http = require('http');
    var url  = require('url');
    var qs = require('qs');
    var Spooky = require('spooky');
    var facebook_uid = 0;

    httpserver = http.createServer(function (request, response) {
        if ( '/favicon.ico' == request.url ) {
            return false;
        }
        request.setEncoding('utf-8');
        query   = qs.parse(url.parse(request.url).query);
        if ( typeof query.url == 'undefined' ) {
            response.write("Param [url] not exists.");
            response.end();
        }

        var spooky = new Spooky({
            child: {
                transport: 'http'
            },
            casper: {
                logLevel: 'debug',
                verbose: true,
                pageSettings: {
                    loadImages:  false,         // The WebPage instance used by Casper will
                    loadPlugins: false,         // use these settings
                    userAgent: 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_5) AppleWebKit/537.4 (KHTML, like Gecko) Chrome/22.0.1229.94 Safari/537.4'
                }
            }
        }, function (err) {
            if (err) {
                e = new Error('Failed to initialize SpookyJS');
                e.details = err;
                throw e;
            }

            spooky.start('https://www.facebook.com/login.php', function() {
                // search for 'casperjs' from google form
                this.emit('fblog', 'Facebook login has loaded.');
                this.fill('form#login_form', {
                    email: '<your-username>',
                    pass:  '<your-password>'
                }, true);
                this.emit('fblog', 'Facebook login form filled.');
            });
            //meta property="al:android:url"
            spooky.thenOpen(query.url)
            spooky.then(function() {
                uid = this.evaluate(function () {
                    var meta_content = document.querySelector('meta[property="al:android:url"]').content;
                    return meta_content.replace('fb://profile/', '');
                });
                this.emit('fblog', 'Find facebook uid: ' + uid);
                this.emit('fbuid', uid);
            });
            spooky.run();
        });

        spooky.on('error', function (e, stack) {
            console.error(e);

            if (stack) {
                console.log(stack);
            }
        });

        // spooky.on('console', function (greeting) {
        //     console.log(greeting);
        // });

        spooky.on('fblog', function (greeting) {
            console.log(greeting);
        });

        spooky.on('fbuid', function (uid) {
            response.write("uid:" + uid);
            response.end();
        });

        spooky.on('log', function (log) {
            if (log.space === 'remote') {
                console.log(log.message.replace(/ \- .*/, ''));
            }
        });
    });
    httpserver.listen('8016', "0.0.0.0");

通过 curl 访问我们的 nodejs 服务：
    
    curl http://localhost:8016/?url=https://www.facebook.com/app_scoped_user_id/72497915498194

以下是 CasperJS 的日志：

![流量拓扑图]({{ site.url }}/uploads/2015/08/facebook-login.png)

![流量拓扑图]({{ site.url }}/uploads/2015/08/facebook-getprofileid.png)