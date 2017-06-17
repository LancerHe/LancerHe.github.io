---
title: PHP cookie和session的分析
author: 谇雨
layout: post
permalink: /cookie-session-discuss.html
views:
  - 293
categories:
  - PHP
tags:
  - cookie
  - php
  - session
---
本文转载至：<http://blog.chinaunix.net/u/27731/showart_259031.html>

<div class="entrybody">
  <p>
    <strong><span style="font-size: 9pt; color: #0080ff; font-family: Arial;" lang="EN-US"> </span></strong>
  </p>
  
  <p>
    <strong> </strong><strong>1. </strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">PHP</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">的</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">COOKIE</span></strong>
  </p>
  
  <p>
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    cookie <span style="font-size: 9pt; font-family: 宋体;">是一种在远程浏览器端储存数据并以此来跟踪和识别用户的机制。</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    PHP<span style="font-size: 9pt; font-family: 宋体;">在</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">http</span><span style="font-size: 9pt; font-family: 宋体;">协议的头信息里发送</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">cookie,</span><span lang="EN-US"> </span><span style="font-size: 9pt; font-family: 宋体;">因此</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> setcookie() </span><span style="font-size: 9pt; font-family: 宋体;">函数必须在其它信息被输出到浏览器前调用，这和对</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> header() </span><span style="font-size: 9pt; font-family: 宋体;">函数的限制类似。</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    <span style="text-decoration: underline;">1.1 </span><strong><span style="font-size: 9pt; font-family: 宋体;">设置</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">cookie</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">:</span></strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    <span> </span><span style="font-size: 9pt; font-family: 宋体;">可以用</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> setcookie() </span><span style="font-size: 9pt; font-family: 宋体;">或</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> setrawcookie() </span><span style="font-size: 9pt; font-family: 宋体;">函数来设置</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> cookie</span><span style="font-size: 9pt; font-family: 宋体;">。也可以通过向客户端直接发送</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">http</span><span style="font-size: 9pt; font-family: 宋体;">头来设置</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <strong>1.1.1</strong><strong> </strong><strong><span style="font-size: 9pt; font-family: 宋体;">使用</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">setcookie()</span></strong><strong><span style="font-size: 9pt; font-family: 宋体;">函数</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">设置cookie:</span></strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    <span style="color: #ff0102;">bool setcookie ( string name [, string value [, int expire [, string path [, string domain [, bool secure [, bool httponly]]]]]] )</span>
  </p>
  
  <p>
    <span style="color: #000102;">name:   cookie</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">变量名</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    value:   cookie<span style="font-size: 9pt; color: #000102; font-family: 宋体;">变量的值</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    expire: <span style="font-size: 9pt; color: #000102; font-family: 宋体;">有效期结束的时间</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span>
  </p>
  
  <p>
    path: <span style="font-size: 9pt; color: #000102; font-family: 宋体;">有效目录</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span>
  </p>
  
  <p>
    domain: <span style="font-size: 9pt; color: #000102; font-family: 宋体;">有效域名</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">顶级域唯一</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    secure: <span style="font-size: 9pt; color: #000102; font-family: 宋体;">如果值为</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">1,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">则</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">cookie</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">只能在</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">https</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">连接上有效</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">如果为默认值</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">0,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">则</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">http</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">和</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">https</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">都可以</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">例子</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">:</span>
  </p>
  
  <p>
    <code><span style="color: #000000;"><span style="color: #0000cc;"><</span><span style="color: #0000cc;">?</span><span style="color: #0000ff;">php</span></span></code>
  </p>
  
  <p>
    <code> </code><code><span style="color: #0000ff;">$</span><span style="color: #008080;">value</span> <span style="color: #0000cc;">=</span> <span style="color: #ff00ff;">'something from somewhere'</span><span style="color: #0000cc;">;</span></code>
  </p>
  
  <p>
    <code><br />
</code>
  </p>
  
  <p>
    <code><span style="color: #ff0000;">setcookie</span><span style="color: #0000cc;">(</span><span style="color: #ff00ff;">"TestCookie"</span><span style="color: #0000cc;">,</span> <span style="color: #0000ff;">$</span><span style="color: #008080;">value</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span> <span style="color: #ff9900;">/* 简单cookie设置 */</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff0000;">setcookie</span><span style="color: #0000cc;">(</span><span style="color: #ff00ff;">"TestCookie"</span><span style="color: #0000cc;">,</span> <span style="color: #0000ff;">$</span><span style="color: #008080;">value</span><span style="color: #0000cc;">,</span> <span style="color: #ff0000;">time</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">+</span>3600<span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span> <span style="color: #ff9900;">/* 有效期1个小时 */</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff0000;">setcookie</span><span style="color: #0000cc;">(</span><span style="color: #ff00ff;">"TestCookie"</span><span style="color: #0000cc;">,</span> <span style="color: #0000ff;">$</span><span style="color: #008080;">value</span><span style="color: #0000cc;">,</span> <span style="color: #ff0000;">time</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">+</span>3600<span style="color: #0000cc;">,</span> <span style="color: #ff00ff;">"/~rasmus/"</span><span style="color: #0000cc;">,</span> <span style="color: #ff00ff;">".example.com"</span><span style="color: #0000cc;">,</span> 1<span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span> <span style="color: #ff9900;">/* 有效目录 /~rasmus,有效域名example.com及其所有子域名 */</span></code>
  </p>
  
  <p>
    <code> </code><code><span style="color: #0000cc;">?</span><span style="color: #0000cc;">></span></code>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">设置多个</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">cookie</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">变量</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">: setcookie(‘var[a]‘,’value’);</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">用数组来表示变量</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">但他的下标不用引号</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">这样就可以用</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">$_COOKIE[‘var’][‘a’]</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">来读取该</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">COOKIE</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">变量</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <strong><img title="更多..." src="http://crackedzone.com/blog/wp-includes/js/tinymce/plugins/wordpress/img/trans.gif" alt="" />1.1.2. </strong><strong><span style="font-size: 9pt; font-family: 宋体;">使用</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">header()</span></strong><strong> </strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">设置cookie</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">;</span></strong><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span><br /> <!--more-->
    
    <br /> <span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">header(“Set-Cookie: name=$value[;path=$path[;</span><em><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">domain=xxx.com</span></em><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">[;&#8230;]]”);</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">后面的参数和上面列出</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">setcookie</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">函数的参数一样</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    比如:
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <code><span style="color: #000000;"><span style="color: #0000ff;">$</span><span style="color: #008080;">value</span> <span style="color: #0000cc;">=</span> <span style="color: #ff00ff;">'something from somewhere'</span><span style="color: #0000cc;">;</span></span></code>
  </p>
  
  <p>
    <code> </code><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">header(“</span><code><span style="color: #000000;"><span style="color: #ff00ff;">Set-Cookie:name=</span></span></code><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">$value</span><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">“);</span>
  </p>
  
  <p>
    <strong> </strong>
  </p>
  
  <p class="MsoNormal" style="text-align: left; text-decoration: underline;">
    <strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">1.2 Cookie的读取:</span></strong>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    直接用php内置超级全局变量 $_COOKIE就可以读取浏览器端的cookie.
  </p>
  
  <p>
    上面例子中设置了cookie<code style="color: #000102;">"TestCookie",现在我们来读取:</code>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <code><span style="color: #000000;">print $_COOKIE<span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'TestCookie'</span><span style="color: #0000cc;">]</span><span style="color: #0000cc;">;</span></span></code>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    COOKIE是不是被输出了?!
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <strong><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">1.3 删除</span></span></strong><strong><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">cookie</span></span></strong>
  </p>
  
  <p>
    <strong> </strong><strong> </strong><span style="font-size: 9pt; color: #000102; font-family: 宋体;">只需把有效时间设为小于当前时间</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">, </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">和把值设置为空</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.例如:</span>
  </p>
  
  <p>
    setcookie(“name”,”&#8221;,time()-1);
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">用</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">header()</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">类似</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <span style="font-weight: bold; color: #0080ff;">1.4 </span><span style="text-decoration: underline;"><span style="font-size: 9pt; font-family: 宋体;">常见问题解决</span></span><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"><span style="font-weight: bold; color: #0080ff;">:</span></span></span>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"><span style="color: #000102;">1) 用setcookie()时有错误提示,可能是因为调用setcookie()前面有输出或空格.也可能你的文档使从其他字符集转换过来,文档后面可能带有BOM签名(就是在文件内容添加一些隐藏的</span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"><span style="color: #000102;">BOM</span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"><span style="color: #000102;">字符).解决的办法就是使你的文档不出现这种情况.还有通过使用ob_start()函数有也能处理一点.</span></span><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span></span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">2) $_COOKIE</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">受</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">magic_quotes_gpc</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">影响</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">可能自动转义</span><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span></span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">3)</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;"> 使用的时候</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">有必要测试用户是否支持</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">cookie</span><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span></span>
  </p>
  
  <p>
    <!–[if !supportLineBreakNewLine]–>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><strong><span style="text-decoration: underline;"><span style="color: #0080ff;">1.5 cookie</span></span></strong></span><strong><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">工作机理</span></span></strong><strong><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">:</span></span></strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">有些学习者比较冲动,没心思把原理研究,所以我把它放后面.</span>
  </p>
  
  <p>
    a) 服务器通过随着响应发送一个<span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">http</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">Set-Cookie</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">头</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">在客户机中设置一个</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">cookie(</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">多个</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">cookie</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">要多个头</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">).</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">b) 客户端自动向服务器端发送一个</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">http</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">cookie</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">头</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">服务器接收读取</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    HTTP/1.x 200 OK
  </p>
  
  <p>
    X-Powered-By: PHP/5.2.1
  </p>
  
  <p>
    <span style="font-weight: bold;">Set-Cookie: </span><code style="font-weight: bold; color: #000102;">TestCookie</code><span style="font-weight: bold; color: #000102;">=</span><code style="font-weight: bold; color: #000102;">something from somewhere</code><span style="font-weight: bold;">; path=/</span>
  </p>
  
  <p>
    Expires: Thu, 19 Nov 2007 18:52:00 GMT
  </p>
  
  <p>
    Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
  </p>
  
  <p>
    Pragma: no-cache
  </p>
  
  <p>
    Content-type: text/html
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    这一行实现了cookie功能,收到这行后
  </p>
  
  <p>
    Set-Cookie: <code style="color: #000102;">TestCookie</code><span style="color: #000102;">=</span><code style="color: #000102;">something from somewhere</code>; path=/
  </p>
  
  <p>
    浏览器将在客户端的磁盘上创建一个cookie文件,并在里面写入:
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <code style="color: #000102;">TestCookie</code><span style="color: #000102;">=</span><code style="color: #000102;">something from somewhere</code>;
  </p>
  
  <p>
    /
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    这一行就是我们用<code><span style="color: #000000;">setcookie<span style="color: #0000cc;">(</span><span style="color: #ff00ff;">'TestCookie'</span><span style="color: #0000cc;">,</span><span style="color: #ff00ff;">'something from somewhere'</span><span style="color: #0000cc;">,</span><span style="color: #ff00ff;">'/'</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></span></code>的结果.也就是用<code><span style="color: #000000;">header<span style="color: #0000cc;">(</span><span style="color: #ff00ff;">'Set-Cookie: TestCookie=something from somewhere; path=/'</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></span></code>的结果.
  </p>
  
  <p>
    <span style="text-decoration: underline;"><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"><!–[endif]–></span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <div class="MsoNormal" style="text-align: center;">
    <p>
      <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
    </p>
    
    <hr size="2" />
  </div>
  
  <p class="MsoNormal" style="text-align: left;">
    <span style="text-decoration: underline;"><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span></span>
  </p>
  
  <p>
    <strong><span style="font-size: 9pt; color: #0080ff; font-family: Arial;" lang="EN-US">2. </span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">PHP</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">的</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">Session</span></strong>
  </p>
  
  <p>
    <strong><br /> </strong>
  </p>
  
  <p>
    <strong> </strong><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">使用过期时间设为</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"></span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">cookie,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">并且将一个称为</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session ID</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的唯一标识符</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">(</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">一长串字符串</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">)</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">,在服务器端同步生成一些session文件(可以自己定义session的保存类型),与用户机关联起来</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.web</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">应用程序存贮与这些</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">相关的数据</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">并且让数据随着用户在页面之间</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">传递</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    访问网站的来客会被分配一个唯一的标识符，即所谓的会话 ID。它要么存放在客户端的 cookie，要么经由 URL 传递。
  </p>
  
  <p>
    会话支持允许用户注册任意数目的变量并保留给各个请求使用。当来客访问网站时，PHP 会自动（如果 session.auto_start 被设为 1）或在用户请求时（由 session_start() 明确调用或 session_register() 暗中调用）检查请求中是否发送了特定的会话 ID。如果是，则之前保存的环境就被重建。
  </p>
  
  <p>
    <strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">2.1 sessionID</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">的传送</span></strong><strong><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US"> </span></span></strong>
  </p>
  
  <p>
    <strong><br /> </strong>
  </p>
  
  <p>
    <strong> </strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">2.1.1 </span></strong><strong><span style="font-size: 9pt; font-family: 宋体;">通过</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">cookie</span></strong><strong><span style="font-size: 9pt; font-family: 宋体;">传送</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">sessin ID</span></strong>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> 使用</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">session_start()</span><span style="font-size: 9pt; font-family: 宋体;">调用session,服务器端在生成session文件的同时,生成session ID哈希值和默认值为</span>PHPSESSID<span style="font-size: 9pt; font-family: 宋体;">的session name,并向客户端发送变量为</span>(默认的是)PHPSESSID(<span style="font-size: 9pt; font-family: 宋体;">session name</span>)<span style="font-size: 9pt; font-family: 宋体;">,值为一个128位的哈希值.服务器端将通过该cookie与客户端进行交互.</span>
  </p>
  
  <p>
    session变量的值经php内部系列化后保存在服务器机器上的文本文件中,和客户端的变量名默认情况下为PHPSESSID的coolie进行对应交互.<span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> 即服务器自动发送了http头:header(‘Set-Cookie: session_name()=session_id()</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">; path=/’);</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; font-family: 宋体;">即</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">setcookie(</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session_name()</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span style="color: #000102;">,</span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session_id())</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">;</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; font-family: 宋体;">当从该页跳转到的新页面并调用</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">session_start()</span><span style="font-size: 9pt; font-family: 宋体;">后</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,PHP</span><span style="font-size: 9pt; font-family: 宋体;">将检查与给定</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">ID</span><span style="font-size: 9pt; font-family: 宋体;">相关联的服务器端存贮的</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; font-family: 宋体;">数据</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">如果没找到</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">则新建一个数据集</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <br style="color: #0080ff;" /><strong>2.1.2 </strong><strong><span style="font-size: 9pt; font-family: 宋体;">通过</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">URL</span></strong><strong><span style="font-size: 9pt; font-family: 宋体;">传送</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span style="color: #0080ff;">session ID</span></span></strong>
  </p>
  
  <p>
    <strong> </strong><strong> </strong><span style="font-size: 9pt; font-family: 宋体;">只有在用户禁止使用</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">cookie</span><span style="font-size: 9pt; font-family: 宋体;">的时候才用这种方法</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">因为浏览器</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">cookie</span><span style="font-size: 9pt; font-family: 宋体;">已经通用</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">为安全起见</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">可不用该方法</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">.<strong> </strong></span>
  </p>
  
  <p>
    <strong> </strong><strong> </strong><a href=”p.php?<span style="color: #000102;"><?php print </span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session_name()</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span style="color: #000102;"> ?></span></span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span style="color: #000102;">=<?php print session_id() ?></span>“>xxx</a>,也可以通过POST来传递session值.</span>
  </p>
  
  <p class="MsoNormal" style="font-weight: bold; color: #0080ff; text-align: left;">
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">2.2 session基本用法实例</span>
  </p>
  
  <p style="margin: 5px; line-height: 150%;">
    <code><span style="color: #000000;"><span style="color: #0000cc;"><</span><span style="color: #0000cc;">?</span><span style="color: #0000ff;">php</span></span></code>
  </p>
  
  <p>
    <code><span style="color: #ff9900;">// page1.php</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff0000;">session_start</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff0000;">echo</span> <span style="color: #ff00ff;">'Welcome to page #1'</span><span style="color: #0000cc;">;</span></p>
<p><span style="color: #ff9900;">/* 创建session变量并给session变量赋值 */</span></p>
<p><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'favcolor'</span><span style="color: #0000cc;">]</span> <span style="color: #0000cc;">=</span> <span style="color: #ff00ff;">'green'</span><span style="color: #0000cc;">;</span></p>
<p><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'animal'</span><span style="color: #0000cc;">]</span> <span style="color: #0000cc;">=</span> <span style="color: #ff00ff;">'cat'</span><span style="color: #0000cc;">;</span></p>
<p></code><code><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'time'</span><span style="color: #0000cc;">]</span> <span style="color: #0000cc;">=</span> <span style="color: #ff0000;">time</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff9900;">// 如果客户端使用cookie,可直接传递session到page2.php</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff0000;">echo</span> <span style="color: #ff00ff;">'<br /><a href="page2.php">page 2</a>'</span><span style="color: #0000cc;">;</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff9900;">// 如果客户端禁用cookie</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff0000;">echo</span> <span style="color: #ff00ff;">'<br /><a href="page2.php?'</span> <span style="color: #0000cc;">.</span> SID <span style="color: #0000cc;">.</span> <span style="color: #ff00ff;">'">page 2</a>'</span><span style="color: #0000cc;">;</span></p>
<p><span style="color: #ff9900;">/*</span></p>
<p>默认php5.2.1下,SID只有在cookie被写入的同时才会有值,如果该session</p>
<p>对应的cookie已经存在,那么SID将为(未定义)空</p>
<p>*/</p>
<p><span style="color: #0000cc;">?</span><span style="color: #0000cc;">></span></p>
<p></code>
  </p>
  
  <p>
    <code><span style="color: #0000cc;"><</span><span style="color: #0000cc;">?</span><span style="color: #0000ff;">php</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff9900;">// page2.php</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff0000;">session_start</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></code>
  </p>
  
  <p>
    <code><span style="color: #ff0000;">print</span> <span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'animal'</span><span style="color: #0000cc;">]</span><span style="color: #0000cc;">;</span> <span style="color: #ff9900;">// 打印出单个session</span></p>
<p><span style="color: #ff0000;">var_dump</span><span style="color: #0000cc;">(</span><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span> <span style="color: #ff9900;">// 打印出page1.php传过来的session值</span></p>
<p></code><code><span style="color: #0000cc;">?</span><span style="color: #0000cc;">></span></code>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">2.3 使用</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">session</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">函数控制页面缓存</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">.</span><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US"> </span></span></strong>
  </p>
  
  <p>
    <strong> </strong><strong> </strong><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> 很多情况下,我们要确定我们的网页是否在客户端缓存,或要设置缓存的有效时间,比如我们的网页上有些敏感内容并且要登录才能查看,如果缓存到本地了,可以直接打开本地的缓存就可以不登录而浏览到网页了.</span><strong><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US"> </span></span></strong>
  </p>
  
  <p>
    <strong> </strong><strong> </strong><strong><span style="text-decoration: underline;"> </span></strong>
  </p>
  
  <p>
    <strong> </strong><strong> </strong><span style="font-size: 9pt; font-family: 宋体;"> 使用</span><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">session_cache_limiter(‘private’);</span><span style="font-size: 9pt; font-family: 宋体;">可以控制页面客户端缓存</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">必须在</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">session_start()</span><span style="font-size: 9pt; font-family: 宋体;">之前调用</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; font-family: 宋体;">更多参数见</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">http://blog.chinaunix.net/u/27731/showart.php?id=258087</span><span style="font-size: 9pt; font-family: 宋体;">的客户端缓存控制</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">控制客户端缓存时间</span><span style="font-size: 9pt; font-family: 宋体;">用</span><span style="font-size: 9pt; font-family: Verdana;"> <span style="color: #ff0102;" lang="EN-US">session_cache_expire(int);</span></span><span style="font-size: 9pt; color: #ff0102; font-family: 宋体;">单位</span><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">(s).</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">也要在</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session_start()</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">前调用</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p>
    <span style="color: #000102;"> 这只是使用session的情况下控制缓存的方法,我们还可以在header()中控制控制页面的缓存.</span>
  </p>
  
  <p>
    <strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">2.4 删除</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">session</span></strong>
  </p>
  
  <p>
    <strong><span style="text-decoration: underline;"> </span></strong>
  </p>
  
  <p>
    <strong> </strong><strong> </strong><strong> </strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">要三步实现</span><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <?php
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">session_destroy();                                      //</span><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;"> 第一步</span><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">: </span><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">删除服务器端</span><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">文件</span><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">这使用</span><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;"> </span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">setcookie(session_name(),”,time()-3600);  // </span><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">第二步</span><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">: </span><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">删除实际的</span><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">session: </span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">$_SESSION = array();                                  // </span><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">第三步</span><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">: </span><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">删除</span><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">$_SESSION</span><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">全局变量数组</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">?></span>
  </p>
  
  <p>
    <strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">2.5 session</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">在</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">PHP</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">大型</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">web</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">应用中的使用</span></strong><strong><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US"> </span></span></strong>
  </p>
  
  <p>
    <strong><br /> </strong>
  </p>
  
  <p>
    <strong> </strong><span style="font-size: 9pt; color: #000102; font-family: 宋体;">对于访问量大的站点</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">用默认的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">存贮方式并不适合</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">目前最优的方法是用数据库存取</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session.</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">这时</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">函数</span><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">bool session_set_save_handler ( callback open, callback close, callback read, callback write, callback destroy, callback gc )</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">就是提供给我们解决这个问题的方案</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span><strong><span style="text-decoration: underline;"> </span></strong>
  </p>
  
  <p>
    <strong> </strong><strong> </strong><span style="font-size: 9pt; color: #000102; font-family: 宋体;">该函数使用的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">6</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">个函数如下</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">:</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p class="MsoNormal" style="margin-left: 47.25pt; text-indent: -18pt; text-align: left;">
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span>1.<span style="font: 7pt 'Times New Roman'; font-size-adjust: none; font-stretch: normal;"> </span></span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">bool open() </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">用来打开会话存储机制</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p class="MsoNormal" style="margin-left: 47.25pt; text-indent: -18pt; text-align: left;">
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span>2.<span style="font: 7pt 'Times New Roman'; font-size-adjust: none; font-stretch: normal;"> </span></span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">bool close() </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">关闭会话存储操作</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p class="MsoNormal" style="margin-left: 47.25pt; text-indent: -18pt; text-align: left;">
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span>3.<span style="font: 7pt 'Times New Roman'; font-size-adjust: none; font-stretch: normal;"> </span></span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">mixde read() </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">从存储中装在</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">数据时使用这个函数</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p class="MsoNormal" style="margin-left: 47.25pt; text-indent: -18pt; text-align: left;">
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span>4.<span style="font: 7pt 'Times New Roman'; font-size-adjust: none; font-stretch: normal;"> </span></span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">bool write() </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">将给定</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session ID</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的所有数据写到存储中</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p class="MsoNormal" style="margin-left: 47.25pt; text-indent: -18pt; text-align: left;">
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span>5.<span style="font: 7pt 'Times New Roman'; font-size-adjust: none; font-stretch: normal;"> </span></span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">bool </span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">destroy() </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">破坏与指定的会话</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">ID</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">相关联的数据</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p class="MsoNormal" style="margin-left: 47.25pt; text-indent: -18pt; text-align: left;">
    <span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span>6.<span style="font: 7pt 'Times New Roman'; font-size-adjust: none; font-stretch: normal;"> </span></span></span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">bool gc() </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">对存储系统中的数据进行垃圾收集</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"> </span>
  </p>
  
  <p class="MsoNormal" style="text-align: left;">
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">例子见php手册</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session_set_save_handler()</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">函数</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">如果用类来处理</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">用</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session_set_save_handler(</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> array(‘className’,’</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">open</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">‘),</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> array(‘className’,’</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">close</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">‘),</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> array(‘className’,’</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">read</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">‘),</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> array(‘className’,’</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">write</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">‘),</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> array(‘className’,&#8217;destroy’),</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> array(‘className’,&#8217;gc’),</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">)</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">调用</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">className</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">类中的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">6</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">个静态方法</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.className</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">可以换对象就不用调用静态方法</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">但是用静态成员不用生成对象</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">性能更好</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <strong><span style="text-decoration: underline;"> </span></strong>
  </p>
  
  <p>
    <strong> </strong><strong> </strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">2.6 常用</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">session</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">函数</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">:</span><span style="text-decoration: underline;"><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US"> </span></span></strong>
  </p>
  
  <p>
    <strong><br /> </strong>
  </p>
  
  <p>
    <strong> </strong><span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">bool   session_start(void); </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">初始化</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span>
  </p>
  
  <p>
    bool   session_destroy(void)<span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">: </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">删除服务器端</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">关联文件</span><span style="font-size: 9pt; color: #ff0102; font-family: 宋体;">。</span>
  </p>
  
  <p>
    string session_id() <span style="font-size: 9pt; color: #000102; font-family: 宋体;">当前</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">id</span>
  </p>
  
  <p>
    string session_name() <span style="font-size: 9pt; color: #000102; font-family: 宋体;">当前存取的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">名称</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">也就是客户端保存</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session ID</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">cookie</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">名称</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">默认</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">PHPSESSID</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">。</span>
  </p>
  
  <p>
    array  session_get_cookie_params() <span style="font-size: 9pt; color: #000102; font-family: 宋体;">与这个</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">相关联的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的细节</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    string session_cache_limiter() <span style="font-size: 9pt; font-family: 宋体;">控制使用</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; font-family: 宋体;">的页面的客户端缓存</span>
  </p>
  
  <p>
    ini    session_cache_expire() <span style="font-size: 9pt; color: #000102; font-family: 宋体;">控制客户端缓存时间</span>
  </p>
  
  <p>
    <span style="color: #ff0102;">bool   session_destroy() </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">删除服务器端保存</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">信息的文件</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">void   session_set_cookie_params ( int lifetime [, string path [, string domain [, bool secure [, bool httponly]]]] )</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">设置与这个</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">相关联的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的细节</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">bool session_set_save_handler ( callback open, callback close, callback read, callback write, callback destroy, callback gc )</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">定义处理</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的函数</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,(</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">不是使用默认的方式</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">)</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #ff0102; font-family: Verdana;" lang="EN-US">bool session_regenerate_id([bool delete_old_session])</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US"> </span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">分配新的</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session id</span>
  </p>
  
  <p>
    <strong><span style="font-size: 9pt; color: #0080ff; font-family: Verdana;" lang="EN-US">2.7 session</span></strong><strong><span style="font-size: 9pt; color: #0080ff; font-family: 宋体;">安全问题</span></strong>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">攻击者通过投入很大的精力尝试获得现有用户的有效会话</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">ID,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">有了会话</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">id,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">他们就有可能能够在系统中拥有与此用户相同的能力</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <span style="font-size: 9pt; color: #000102; font-family: 宋体;">因此</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">我们主要解决的思路是效验</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">session ID</span><span style="font-size: 9pt; color: #000102; font-family: 宋体;">的有效性</span><span style="font-size: 9pt; color: #000102; font-family: Verdana;" lang="EN-US">.</span>
  </p>
  
  <p>
    <span style="color: #0000cc;"><</span><span style="color: #0000cc;">?</span><span style="color: #0000ff;">php</span>
  </p>
  
  <p style="margin: 5px; line-height: 150%;">
    <code><span style="color: #000000;"><span style="color: #0000ff;">if</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">!</span><span style="color: #ff0000;">isset</span><span style="color: #0000cc;">(</span><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'user_agent'</span><span style="color: #0000cc;">]</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">{</span></span></code>
  </p>
  
  <p>
    <code><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'user_agent'</span><span style="color: #0000cc;">]</span> <span style="color: #0000cc;">=</span> <span style="color: #0000ff;">$</span><span style="color: #008080;">_SERVER</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'REMOTE_ADDR'</span><span style="color: #0000cc;">]</span><span style="color: #0000cc;">.</span><span style="color: #0000ff;">$</span><span style="color: #008080;">_SERVER</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'HTTP_USER_AGENT'</span><span style="color: #0000cc;">]</span><span style="color: #0000cc;">;</span></code>
  </p>
  
  <p>
    <code> </code><code><span style="color: #0000cc;">}</span></code>
  </p>
  
  <p>
    <code><br />
</code>
  </p>
  
  <p>
    <code><span style="color: #ff9900;">/* 如果用户session ID是伪造 */</span></code>
  </p>
  
  <p>
    <code><span style="color: #0000ff;">elseif</span> <span style="color: #0000cc;">(</span><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'user_agent'</span><span style="color: #0000cc;">]</span> <span style="color: #0000cc;">!</span><span style="color: #0000cc;">=</span> <span style="color: #0000ff;">$</span><span style="color: #008080;">_SERVER</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'REMOTE_ADDR'</span><span style="color: #0000cc;">]</span> <span style="color: #0000cc;">.</span> <span style="color: #0000ff;">$</span><span style="color: #008080;">_SERVER</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'HTTP_USER_AGENT'</span><span style="color: #0000cc;">]</span><span style="color: #0000cc;">)</span> <span style="color: #0000cc;">{</span></code>
  </p>
  
  <p>
    <code> </code><code><span style="color: #000000;"><span style="color: #ff0000;">session_regenerate_id</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></span></code>
  </p>
  
  <p>
    <code><span style="color: #0000cc;">}</span></code>
  </p>
  
  <p>
    <code> </code><code><span style="color: #0000cc;">?</span><span style="color: #0000cc;">></span></code>
  </p>
  
  <p class="MsoNormal">
    <p>
      <strong>2.8 Session</strong><strong><span style="font-size: 9pt; font-family: 宋体;">通过</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">cookie</span></strong><strong><span style="font-size: 9pt; font-family: 宋体;">传递和通过SID传递的不同</span></strong><strong><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US"><span style="color: #0080ff;">:</span></span></strong>
    </p>
    
    <p>
      <strong> </strong><strong> </strong><span style="font-size: 9pt; font-family: 宋体;">在</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">php5.2.1</span><span style="font-size: 9pt; font-family: 宋体;">的</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; font-family: 宋体;">的默认配置的情况下</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">当生成</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">session</span><span style="font-size: 9pt; font-family: 宋体;">的同时</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">服务器端将在发送</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">header set-cookie</span><span style="font-size: 9pt; font-family: 宋体;">同时</span><span style="font-size: 9pt; font-family: 宋体;">生成预定义超级全局变量</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">SID(</span><span style="font-size: 9pt; font-family: 宋体;">也就是说</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">写入</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">cookie</span><span style="font-size: 9pt; font-family: 宋体;">和抛出</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">SID</span><span style="font-size: 9pt; font-family: 宋体;">是等价的</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">.),</span><span style="font-size: 9pt; font-family: 宋体;">当</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">$_COOKIE[&#8216;PHPSESSID&#8217;]</span><span style="font-size: 9pt; font-family: 宋体;">存在以后</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,</span><span style="font-size: 9pt; font-family: 宋体;">将不再写入</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">cookie,</span><span style="font-size: 9pt; font-family: 宋体;">也不再生成超级全局变量</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">SID,</span><span style="font-size: 9pt; font-family: 宋体;">此时</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">,SID</span><span style="font-size: 9pt; font-family: 宋体;">将是空的</span><span style="font-size: 9pt; font-family: Verdana;" lang="EN-US">.</span>
    </p>
    
    <p>
      <span style="font-weight: bold; color: #0080ff;">2.9 session使用实例</span>
    </p>
    
    <p style="margin: 5px; line-height: 150%;">
      <code><span style="color: #000000;"><span style="color: #0000cc;"><</span><span style="color: #0000cc;">?</span><span style="color: #0000ff;">php</span></span></code>
    </p>
    
    <p>
      <code><span style="color: #ff9900;">/**</span></code>
    </p>
    
    <p>
      <code>* 效验session的合法性</code>
    </p>
    
    <p>
      <code>*</p>
<p>*/</p>
<p><span style="color: #0000ff;">function</span> sessionVerify<span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span> <span style="color: #0000cc;">{</span></p>
<p><span style="color: #0000ff;">if</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">!</span><span style="color: #ff0000;">isset</span><span style="color: #0000cc;">(</span><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'user_agent'</span><span style="color: #0000cc;">]</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">{</span></p>
<p><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'user_agent'</span><span style="color: #0000cc;">]</span> <span style="color: #0000cc;">=</span> <span style="color: #ff0000;">MD5</span><span style="color: #0000cc;">(</span><span style="color: #0000ff;">$</span><span style="color: #008080;">_SERVER</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'REMOTE_ADDR'</span><span style="color: #0000cc;">]</span></p>
<p><span style="color: #0000cc;">.</span><span style="color: #0000ff;">$</span><span style="color: #008080;">_SERVER</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'HTTP_USER_AGENT'</span><span style="color: #0000cc;">]</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></p>
<p><span style="color: #0000cc;">}</span></p>
<p><span style="color: #ff9900;">/* 如果用户session ID是伪造,则重新分配session ID */</span></p>
<p><span style="color: #0000ff;">elseif</span> <span style="color: #0000cc;">(</span><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'user_agent'</span><span style="color: #0000cc;">]</span> <span style="color: #0000cc;">!</span><span style="color: #0000cc;">=</span> <span style="color: #ff0000;">MD5</span><span style="color: #0000cc;">(</span><span style="color: #0000ff;">$</span><span style="color: #008080;">_SERVER</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'REMOTE_ADDR'</span><span style="color: #0000cc;">]</span></p>
<p><span style="color: #0000cc;">.</span> <span style="color: #0000ff;">$</span><span style="color: #008080;">_SERVER</span><span style="color: #0000cc;">[</span><span style="color: #ff00ff;">'HTTP_USER_AGENT'</span><span style="color: #0000cc;">]</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">)</span> <span style="color: #0000cc;">{</span></p>
<p><span style="color: #ff0000;">session_regenerate_id</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></p>
<p><span style="color: #0000cc;">}</span></p>
<p></code><code><span style="color: #0000cc;">}</span></code>
    </p>
    
    <p>
      <code><br />
</code>
    </p>
    
    <p>
      <code><span style="color: #ff9900;">/**</span></code>
    </p>
    
    <p>
      <code>* 销毁session</code>
    </p>
    
    <p>
      <code>* 三步完美实现,不可漏</code>
    </p>
    
    <p>
      <code>*</p>
<p>*/</p>
<p><span style="color: #0000ff;">function</span> sessionDestroy<span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span> <span style="color: #0000cc;">{</span></p>
<p><span style="color: #ff0000;">session_destroy</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></p>
<p><span style="color: #ff0000;">setcookie</span><span style="color: #0000cc;">(</span><span style="color: #ff0000;">session_name</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">,</span><span style="color: #ff00ff;">''</span><span style="color: #0000cc;">,</span><span style="color: #ff0000;">time</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">-</span>3600<span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></p>
<p><span style="color: #0000ff;">$</span><span style="color: #008080;">_SESSION</span> <span style="color: #0000cc;">=</span> <span style="color: #ff0000;">array</span><span style="color: #0000cc;">(</span><span style="color: #0000cc;">)</span><span style="color: #0000cc;">;</span></p>
<p><span style="color: #0000cc;">}</span></p>
<p></code><code><span style="color: #0000cc;">?</span><span style="color: #0000cc;">></span></code>
    </p>
    
    <p style="margin: 5px; line-height: 150%;">
      <p style="margin: 5px; line-height: 150%;">
        <code><span style="color: #000000;"><span style="color: #0000cc;">注明:</span></span></code>
      </p>
      
      <p>
        <code> </code>
      </p>
      
      <p style="margin: 5px; line-height: 150%;">
        <code><span style="color: #000000;"><span style="color: #0000cc;"> session 出现头信息已经发出的原因与cookie一样.</span></span></code>
      </p>
      
      <p>
        <code> </code><code><span style="color: #000000;"><span style="color: #0000cc;"> 在php5中,所有php session 的注册表配置选项都是编程时可配置的,一般情况下,我们是不用修改其配置的.要了解php的session注册表配置选项,请参考手册的Session 会话处理函数处.</span></span></code>
      </p>
      
      <div>
        session的保存数据的时候，是通过系列化$_SESSION数组来存贮，所以有系列化所拥有的问题，可能有特殊字符的值要用base64_encode函数编码，读取的时候再用base64_decode解码
      </div>
      
      <div class="entrymeta2">
        <span class="meta-comment-add"> </span>
      </div>
      
      <p>
        <!-- [entrymeta2] -->
      </p></div>