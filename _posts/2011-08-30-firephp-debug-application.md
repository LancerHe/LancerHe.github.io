---
title: FirePHP开发中的调试应用
author: 谇雨
layout: post
permalink: /firephp-debug-application.html
views:
  - 188
categories:
  - PHP
  - Software
tags:
  - firephp
  - 应用
  - 调试
---
1. 安装Firebug插件和FirePHP插件，FirePHP只是Firebug的一个扩展。

2. 登陆FirePHP的主页：<a href="http://www.firephp.org/" target="_blank">http://www.firephp.org/</a> 下载FirePHPCore Server Library

3. 解压缩取得FirePHPCore文件夹下

    fb.php                   //php5 面向过程方式调试  
    FirePHP.class.php        //php5 面向对象方式调试  
    fb.php4                  //php4 面向过程方式调试  
    FirePHP.class.php4       //php4 面向对象方式调试  
选择自己想要的调试模式的文件。

例如：

<pre class="lang:default decode:true " title="框架中调用FirePHP类" >//使用FirePHP.class.php
$this->load->library('firephp');   //载入firephp类
$this->firephp->log($this->pagination);  //调用该类的log方法</pre>

之后就可以在Firebug的控制台看到这个对象或者数据的结构，但并不影响整个页面HTML和Dom的结构。

[<img class="aligncenter size-full wp-image-485" title="FirePHP" src="http://crackedzone.com/blog/wp-content/uploads/2011/08/FirePHP1.jpg" alt="" width="520" height="360" />][1][  
][2]

 [1]: http://crackedzone.com/blog/wp-content/uploads/2011/08/FirePHP1.jpg
 [2]: http://crackedzone.com/blog/wp-content/uploads/2011/08/FirePHP.jpg