---
title: PHP中单例模式数据库应用
author: 谇雨
layout: post
permalink: /php-singleton-application.html
views:
  - 437
categories:
  - PHP
tags:
  - instance
  - php
  - 单例模式
---
最近接触到一个新名词：单例模式。  
刚开始一直百思不得其解，百度的结果居然是平时看到的 Db::getInstance()-> ，原来这个就是所谓的单例模式。  
先看我们平时使用的方法

<pre class="lang:php decode:true " >class Category{
	//定义一个获取分类方法
	function getCategory() {
		//这时我们不得不重新初始化一个数据库句柄,试想多个应用场景下，这样的代码是多么可怕啊？！ 
		$db = new DB();
		$db->query($sql);
		//当然也可以使用全局变量
		global $db;
		//但一般在OPP编程中，不使用这样的方法，同时global存在安全隐患
		//同时单例模式恰恰是对全局变量的一种改进，避免了那些存储唯一实例的全局变量污染命名空间
	}
}</pre>

倘若采用单例模式

<pre class="lang:php decode:true " >class Category{
	//定义一个获取分类方法
	function getCategory() {
		//所有的应用情景只有一个数据库句柄资源，嘿嘿，效率老高了， 
		//资源也大大的得到节省，代码简洁明了：）
		$query = Db::getInstance()->query($sql);
		$array = Db::getInstance()->fetch_array($query);
	}
}</pre>

<!--more-->

<p style="text-indent: 2em;">
  首先还是要知道明确单例模式这个概念。所谓单例，就是只有一个实例。<br /> 作为对象的创建模式，单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，<br /> 所以这个类我们称之为单例类。
</p>

一般来说使用单例模式的情况有如下：  
一是某个类只能有一个实例；  
二是它必须自行创建这个实例；  
三是它必须自行向整个系统提供这个实例。

这么说来我们连接数据库类倘若使用单例模式，可以对系统的资源起到节省作用，同时可以避免实例化类。

下面写个单例类

<pre class="lang:php decode:true " >class MySQL{ 
	/** 
	*  静态成员变量 保存全局实例 
	*  @access private 
	*/
	static private $_instance = NULL;  
  /** 
    * 私有化构造函数，防止外界实例化对象 
		*/ 
	private function  __construct() {} 
	/** 
	 *  私有化克隆函数，防止外界克隆对象 
	 */ 
	private function  __clone(){} 
	/** 
	 *  静态方法, 单例统一访问入口 
	 *  @return  object  返回对象的唯一实例 
	 */ 
	static public function getInstance() { 
		if (is_null(self::$_instance) || !isset(self::$_instance)) { 
			 self::$_instance = new MySQL(); 
		} 
		return self::$_instance; 
	}   
	/** 
	 * 数据库其他方法 ...... 
	 */ 
	public function connect() {} 
	public function query() {} 
} </pre>

从以上代码中，我们总结出 PHP 单例模式实现的核心要点有如下三条：  
1. 需要一个保存类的唯一实例的静态成员变量（通常为$_instance 私有变量）  
2. 构造函数和克隆函数必须声明为私有的，这是为了防止外部程序 new类从而失去单例模式的意义  
3. 必须提供一个访问这个实例的公共的静态方法（通常为 getInstance 方法），从而返回唯一实例的  
一个引用