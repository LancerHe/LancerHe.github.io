---
title: PHP中字符串定义小结
author: 谇雨
layout: post
permalink: /php-string-define-summary.html
views:
  - 51
categories:
  - PHP
tags:
  - php
  - 反引号
  - 定界符
  - 引号
---
一、双引号(&#8220;&#8221;)定义  
1、可以在双引号中执行变量  
2、可以使用转义字符  
二、单引号(&#8221;)定义  
3、不能在单引号中执行变量  
4、只能使用部分转义字符  
<!--more-->

  
三、反引号(&#8220;)定义  
1、反引号内可以定义字符  
2、但是只能使用服务器命令  
四、定界符(<<<)定义  
1、定义方式与双引号一致  
2、可以在里面存在各种引号，不需要转义引号

<?php
$num = 10;
//双引号
$str = "I am a boy , I am $num years old."; 
//I am a boy , I am 10 years old.

$str = "I am a boy ,\r\t I am \"$num\" years old."; 
//I am a boy 
//		I am "10" years old.

//单引号
$str = 'I am a boy , I am $num years old.';
//I am a boy , I am $num years old. 不能解析变量$num

$str = 'I am a boy ,\r\t I am \'$num\' years old.';
//I am a boy ,\r\t I am '$num' years old.  不能解析\r\n 等...

//反引号
$str = `ipconfig /all`;
$str = `dir`;
//echo $str 相当于在windows的cmd命令提示框下输入信息，也可以在Unix/Linux中用

//定界符
$str = <<<EOT
	<table id="id1" border="1">
		<tr>
			<td>这是一个表格</td>
		</tr>
	</table>
EOT;
echo $str;
// 以<<<{name}开头,{name};结束。{name}可以自己定义，只要一致即可，但{$name}前后不能有空格和其他符号，内部可以直接存在单引号双引号，\r\n等，不需转义.
?>