---
title: PHP自动加载对象
author: 谇雨
layout: post
permalink: /php-autoload-object.html
views:
  - 78
categories:
  - PHP
tags:
  - php
  - 类
  - 自动
---
很多开发者写面向对象的应用程序时对每个类的定义建立一个 PHP 源文件。一个很大的烦恼是不得不在每个脚本（每个类一个文件）开头写一个长长的包含文件列表。  
在 PHP 5 中，不再需要这样了。可以定义一个 __autoload 函数，它会在试图使用尚未被定义的类时自动调用。通过调用此函数，脚本引擎在 PHP 出错失败前有了最后一个机会加载所需的类。

> **Note: **在 __autoload 函数中抛出的异常不能被 catch 语句块捕获并导致致命错误。 

> **Note: **如果使用 PHP 的 CLI 交互模式 时，Autoloading 不存在。 

```
function __autoload($className){
	if (!class_exists($className, false))
		require_once(dirname(__FILE__).'/../classes/'.$className.'.php');
}
```