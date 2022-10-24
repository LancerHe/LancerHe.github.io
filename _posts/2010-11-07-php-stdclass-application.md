---
title: PHP stdClass 应用
author: 谇雨
layout: post
permalink: /php-stdclass-application.html
views:
  - 928
categories:
  - PHP
tags:
  - php
  - stdClass
---
最近在某源码中发现了这个类：stdClass，发现该源码中并没有声明过这个类，再查手册也没发现，于是上网找下资料，原来stdClass在PHP5才开始被流行。而stdClass也是zend的一个保留类。也可以理解为stdClass是PHP的一个基类，所有的类几乎都继承这个类，所以任何时候都可以被new，可以让这个变量成为一个object。同时，这个基类又有一个特殊的地方，就是没有方法。  
当我们声明一个数组时候：

$arrTemp = array();   
$arrTemp['a'] = 1;   
$arrTemp['b'] = 2;   
$arrTemp['c'] = 3;  

如果声明一个对象，操作就多了一些

$arrTemp = array(); 
$objTemp = (object)$arrTemp;   
$objTemp->a = 1;   
$objTemp->b = 2;   
$objTemp->c = 3; 

<!--more-->

  
新建空数组只要$arrTemp = array();  
新建空对像却要先有个class obj{}  
倘若使用 stdClass

$user = new stdClass();   
$user->name = 'gouki';   
$user->id = '12';
$user->group = 'manager'

我们可以这么理解一下，正因为PHP5的对象的独特性，对象在任何地方被调用，都是引用地址型的，所以相对消耗的资源会少一点。在其它页面为它赋值时是直接修改，而不是引用一个拷贝。  
例如：

$user = new stdClass();   
$user->name = 'gouki';   
$myUser = $user;   
$myUser->name = 'flypig';  

如果在PHP4时代，这样的代码就是在消耗系统资源。因为：

> $myUser = $user;

这是创建了一个拷贝。所以，在PHP4的时候，都是这样使用：

> $myUser = & $user;

但这又与数组有什么差别呢？然而数组的每次被引用（$a = $b），其实都是创建了一个副本。无论如何都多占了一次内存。