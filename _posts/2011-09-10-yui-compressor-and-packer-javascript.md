---
title: 使用YUI Compressor 和 /packer/ 压缩JavaScript
author: 谇雨
layout: post
permalink: /yui-compressor-and-packer-javascript.html
views:
  - 504
categories:
  - 站长日记
tags:
  - javascript
  - jquery
  - 压缩
  - 打包
---
Javascript压缩后放在我们的网站上，能优化读取速度，就像我们压缩图片格式一样。降低javascript脚本代码的容量将使得你的站点提高载入速度。同时访问者也得到更好的体验。

而javascript压缩有一般两种情况，用jquery举例：

jquery-1.4.2.js                       这个是原版， 用于平时开发的时候，或者自己分析源码用。

jquery-1.4.2.min.js                   min压缩，当服务器没有开启gzip的时候用。

jquery-1.4.2.pack.js                  pack压缩，通过eval处理打包而成，开启gzip的时候读取速度比较快。

而现在我们需要将javascript文件压缩，我将用到如下两个工具 [YUI Compressor][1] 和 Dean Edward’s[/packer/][2]。  
<!--more-->

### 准备阶段：

1.  准备一个文件夹用于操作: D:\www\projects\
2.  [下载 YUI Compressor][3]. 目前最新的是V2.4.6。
3.  提取出&#8221;yuicompressor-2.4.6/build/yuicompressor-2.4.6.jar&#8221;, 更名为&#8221;yuicompressor.jar&#8221;，移至&#8221;D:\www\projects\&#8221;， 这个jar包用于生成min文件。前提是要安装java至少1.4，这个官方有说明。
4.  [下载 rhino1.7R3.zip][4].
5.  提取出&#8221;rhino1_7R3/js.jar&#8221;, 更名为&#8221;jspacker.jar&#8221;，移至&#8221;D:\www\projects\&#8221;， 这个jar包用于把min文件打包成pack文件。
6.  下载 [packer.js][5] (David McNab写的打包JS文件). 同样拷贝到&#8221;D:\www\projects\&#8221;

### 写入注册表compressor.reg

为了更加方便，我们设置当选择js文件右键时候就会出现Compressor JS选项，点击将执行一个我们写的批处理，让它自动打包。

[<img class="aligncenter size-full wp-image-498" title="packer1" src="http://crackedzone.com/blog/wp-content/uploads/2011/09/packer1.jpg" alt="" width="424" height="251" />][6]

compressor.reg 源代码，根据需求设置

<pre class="lang:sh decode:true " >Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\JSFile\shell\Compress JS]
@="Compressor JS"

[HKEY_CLASSES_ROOT\JSFile\shell\Compress JS\Command]
@="D:\\www\\projects\\compressor.bat %1"</pre>

### 创建一个批处理compressor.bat

这个批处理是把当前文件先打包成min格式，再压缩成pack格式

[<img class="aligncenter size-full wp-image-499" title="packer2" src="http://crackedzone.com/blog/wp-content/uploads/2011/09/packer2.jpg" alt="" width="492" height="176" />][7]

compressor.bat 源代码，根据需求设置

<pre class="lang:sh decode:true " >@echo off

java -jar D:\www\projects\yuicompressor.jar %1 -o %~n1.min%~x1
java -jar D:\www\projects\jspacker.jar D:\www\projects\packer.js %~n1.min%~x1 %~n1.pack%~x1
</pre>

当然之前在网上看到很多批处理能把文件夹内所有的文件一起打包，个人觉得那个太智能了，遍历的文件夹也很多，导致我之前.svn文件夹下的文件也被打包了，很是郁闷，后来还是决定用单个文件的形式，毕竟我打包的数目也不多。

[<img class="aligncenter size-full wp-image-501" title="packer3" src="http://crackedzone.com/blog/wp-content/uploads/2011/09/packer3.jpg" alt="" width="434" height="164" />][8]  
一共用的文件差不多是5个，点此下载：[Automate\_JavaScript\_compression][9]

 [1]: http://www.julienlecomte.net/blog/2007/08/11/
 [2]: http://dean.edwards.name/packer/
 [3]: http://www.julienlecomte.net/yuicompressor/
 [4]: http://www.mozilla.org/rhino/download.html
 [5]: http://johannburkard.de/resources/Johann/packer.js
 [6]: http://crackedzone.com/blog/wp-content/uploads/2011/09/packer1.jpg
 [7]: http://crackedzone.com/blog/wp-content/uploads/2011/09/packer2.jpg
 [8]: http://crackedzone.com/blog/wp-content/uploads/2011/09/packer3.jpg
 [9]: http://crackedzone.com/blog/wp-content/uploads/2011/09/Automate_JavaScript_compression.zip