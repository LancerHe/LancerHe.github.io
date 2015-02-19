---
title: CI框架验证码的使用小结
author: 谇雨
layout: post
permalink: /ci-validcode-summary.html
views:
  - 3806
categories:
  - CodeIgniter
tags:
  - CI
  - 验证码
---
<p style="text-indent: 2em;">
  用CI框架做项目的时候有些时候难免需要验证码，因此特地去官方论坛上搜了一个验证码类Authcode,由于有些改动，就先放在附件中。
</p>

<p style="text-indent: 2em;">
  将这个类放在application下，其实这个类还是非常完善的，可以设定字符数目，删除线，杂点之类，甚至还能自定义字体，使用一般输出图片或者选择js输出&#8230;&#8230;由于该类使用的session是CI内部的session，之前要先加载CI的session类。
</p>

<p style="text-indent: 2em;">
  因此先在controllers下建立一个validation的控制器用来生成验证码图片
</p>

<!--more-->

<pre class="lang:php decode:true " >&lt;?php
class Validation extends Controller{
	function __construct(){
		parent::Controller();
		//加载验证码类
		$this-&gt;load-&gt;library('authcode');
	}
	function show(){
		$this-&gt;authcode-&gt;show();
	}
	function script(){
		$this-&gt;authcode-&gt;showScript();
	}
	function check(){
		//调用authcode类中的check方法 判断验证码输入是否正确，ajax回传...
		if ($this-&gt;authcode-&gt;check(strtolower($this-&gt;uri-&gt;segment(3)))) {
			echo 1;//成功
		} else {
			echo 2;
		}
	}
}
?&gt;</pre>

在views下建立一个视图 register,直接显示控制器Validation中的script方法.

<pre class="lang:xhtml decode:true " >&lt;script type="text/javascript" src="&lt;?=site_url('validation/script')?&gt;"&gt;&lt;/script&gt;</pre>

附件下载：[Authcode CI验证码类][1]

 [1]: http://crackedzone.com/blog/wp-content/uploads/2010/10/libraries.zip