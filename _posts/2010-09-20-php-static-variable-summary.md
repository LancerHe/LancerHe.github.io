---
title: PHP函数中静态变量的使用方法和技巧
author: 谇雨
excerpt: |
  <p style="text-indent: 25px;">首先理解静态变量的区别，声明一个函数test()</p>
  <coolcode lang="php" linenum="off"><?php
  function test(){
  	$num = 0;
  	echo $num . "<br />";
  	$num++;
  }
  
  test();test();test();test();test();
  ?>
  </coolcode>
layout: post
permalink: /php-static-variable-summary.html
views:
  - 468
categories:
  - PHP
tags:
  - php
  - 内存
  - 函数
  - 静态变量
---
首先理解静态变量的区别，声明一个函数test()

<pre class="lang:php decode:true " ><?php
function test(){
	$num = 0;
	echo $num . "<br />";
	$num++;
}

test();test();test();test();test();
?></pre>

<!--more-->

  
在浏览器的上输出的是五个0，因为PHP在调用函数的时候，将函数内部变量$num分配一个内存地址，当这个函数使用完后就把$num的内存释放掉了，第二次又新分配一个内存地址。所以无论掉用多少次输出的都是0。  
但是假如声明一个静态变量。

<pre class="lang:php decode:true " ><?php
function test(){
	static $num = 0;
	echo $num . "<br />";
	$num++;
}

test();test();test();test();test();
?></pre>

则浏览器上会出现0,1,2,3,4。静态变量将被同一个调用函数之间共享，并且在在第一次调用的时候会初始化变量的值，也就是第一次调用函数的时候$num被赋值为0，而第二次调用了的时候将被跳过static $num=0这句。  
为了方便理解应用，从PHP100网站看到过一个例子，利用static实现表格的颜色隔行显示

<pre class="lang:php decode:true " ><?php
function getcolor() {
	static $colorvalue;
	if($colorvalue = '#FFF')
		$colorvalue = '#000';
	else
		$colorvalue = '#FFF';
	return $colorvalue;
}
echo "<table border=1>";
for ($i=0;$i<10;$i++) {
	$bgcolor = getcolor();
	echo "“<tr bgcolor=$bgcolor>";
	echo "<td>$i</td>";
	echo "</tr>";
}
?></pre>

说明：此程序中定义了一个静态变量static $colorvalue意思是在函数调用结束后， 此变量$colorvalue还保留值，没有消失。当再次调用getcolor()函数时，变量$colorvalue的值是上次函数调用结束时$colorvalue的值。