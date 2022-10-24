---
title: 'PHP设计模式 -- 工厂模式应用学习心得'
author: 谇雨
layout: post
permalink: /php-factory-model-study.html
views:
  - 201
categories:
  - PHP
tags:
  - php
  - 工厂模式
  - 设计模式
---
工厂模式是指使用一个工厂类来创建其他对象的方法的类。通常用来返回符合类似接口的不同的类。工厂的一种常见用法就是创建多态的提供者，从而允许我们基于应用程序逻辑或者配置设置来决定应该实例化哪一个类。

在PHP应用程序中，通常会使用关系型数据库来储存应用程序创建和引用的数据。使用数据库的最大问题就是不能用一种可移植并且可互换的方法来它们打交道。加入我们把一种数据库移植到另一种的数据库上，那么可能需要重构大部分的代码。然而使用工厂模式就能避免这个问题的发生。

加入我们现在有两个数据库类

class PGSQL {
	function __construct() {}
  function connect() {}
}

class MYSQL {
	function __construct() {}
  function connect() {}
}
</coolcode>
<!--more-->
<coolcode lang="php" linenum="off">

//我们现在创建一个工厂类

class Database {
	function static function load() {
  	$type = loadConfig(); //从配置文件中找出数据库类型
    switch ($type) {
    	case 'PGSQL':
      	return new PGSQL();
        break;
      case 'MYSQL':
      	return new MYSQL();
        break;
    }
  }
}

// -- 用法 -- 

$db = Database::load();
$db->connect();

通过工厂类Database, 通过不同的配置实例化不同的数据库类。

工厂模式的作用在于，可以根据输入参数或者应用程序的配置不同来创建一种专门用来实例化并返回其他类的实例的类。通过一个工厂方法返回一个对象