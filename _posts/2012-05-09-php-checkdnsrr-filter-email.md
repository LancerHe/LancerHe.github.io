---
title: PHP利用checkdnsrr过滤更有效的邮箱地址
author: 谇雨
layout: post
permalink: /php-checkdnsrr-filter-email.html
views:
  - 166
categories:
  - PHP
tags:
  - checkdnsrr
  - email
  - php
---
PHP检验邮箱大部分都通过正则，

但是正则依然不能过滤出一些无效的邮件。如：atin@12232ssd.com, 像12232ssd.com这样的域名很可能是不存在的，邮箱存在的前提条件就是域名必须存在。

庆幸的是PHP可以具有检查域名是否有效的函数checkdnsrr：

> bool checkdnsrr ( string $host [, string $type = &#8220;mx&#8221; ] )

注意的是：该函数在php5.3版本下只在Linux下起作用。

function checkEmail($email){
	$exp = "/^[0-9a-zA-Z]+([_a-z0-9\-\.]+)*@[a-zA-Z0-9]{2,}(?:[-.][a-zA-Z0-9]+)*\.[a-zA-Z]{2,}$/";
		if( preg_match($exp, $email) ){
			if(checkdnsrr(array_pop(explode("@",$email)),"MX"))
				return true;
			else
			return false;
		} else {
			return false;
		}
}