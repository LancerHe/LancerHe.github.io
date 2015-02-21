---
title: 学习CI框架之MVC模型开发小记(一)
author: 谇雨
excerpt: "今天开始接触CodeIgniter框架，这个所谓的简单，也是容易学习的MVC框架。本来也想学Zend Framework框架的，下载解压完就20个兆，在一看里面文件的一大堆。晕倒掉了。。仔细想了想我学习PHP还不到几个月还是从简单的框架开始，也能逐步让我对 M-V-C 模型产生一定的认识。 CI其实很小，除掉用户手册，差不多就1M左右吧。所谓MVC模式： models(模型)：system/application/models/ views(视图)：system/application/views/ controllers(控制器)：system/application/controllers/ 首先找到配置文件:system/application/config/config.php 设置下根目录就OK了:$config['base_url'] = “localhost/ci”; 打开http://localhost/ci/index.php OK成功运行了。。 由于system/application/config/routes.php中设置$route['default_controller'] = ......"
layout: post
permalink: /ci-study-mvc-summary-1.html
views:
  - 441
categories:
  - CodeIgniter
tags:
  - CI
  - MVC
  - 控制器
  - 框架
---
<p style="text-indent: 25px;">
  今天开始接触CodeIgniter框架，这个所谓的简单，也是容易学习的MVC框架。本来也想学Zend Framework框架的，下载解压完就20个兆，在一看里面文件的一大堆。晕倒掉了。。仔细想了想我学习PHP还不到几个月还是从简单的框架开始，也能逐步让我对 M-V-C 模型产生一定的认识。
</p>

CI其实很小，除掉用户手册，差不多就1M左右吧。所谓MVC模式：

models(模型)：system/application/models/  
views(视图)：system/application/views/  
controllers(控制器)：system/application/controllers/

首先找到配置文件:system/application/config/config.php  
设置下根目录就OK了:$config[&#8216;base_url&#8217;] = &#8220;localhost/ci&#8221;;  
打开http://localhost/ci/index.php OK成功运行了。。  
由于system/application/config/routes.php中设置$route[&#8216;default_controller&#8217;] = &#8220;welcome&#8221;;  
默认页面是控制器下的welcome.php  
其实刚刚访问的  
http://localhost/ci/index.php等价于  
http://localhost/ci/index.php/welcome

<!--more-->

  
为此开始第一个实例：模仿welcome在控制器下建立一个Blog类，但要注意类名第一个字母要大写的

<pre class="lang:php decode:true " ><?php
class Blog extends Controller{
	function __construct(){
		parent::Controller();
	}
	
	function category($id){
		echo $id;
	}
}
?></pre>

现在访问:http://localhost/ci/index.php/blog/category/2  
浏览器输出:2  
可以对应过来：http://localhost/ci/index.php/<span style="color:red">class</span>/<span style="color:purple">function</span>/<span style="color:blue">ID</span>  
1.第一段表示调用控制器类。  
2.第二段表示调用类中的函数或方法。  
3.第三及更多的段表示的是传递给控制器的参数，如 ID 或其他各种变量。

默认情况下，index.php 文件将被包含在你的 URL 中，看起来很不爽。。只要通过.htaccess删除

<pre class="lang:apache decode:true " >RewriteEngine on
RewriteCond $1 !^(index\.php)
RewriteRule ^(.*)$ index.php/$1 [L]</pre>

因为我的CI不在根目录，所以使用RewriteRule ^(.*)$ index.php/$1 [L]  
OK.访问 http://localhost/ci/blog/category/2 成功