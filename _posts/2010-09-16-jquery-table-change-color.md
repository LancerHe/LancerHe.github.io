---
title: jQuery实现表格隔行变色及鼠标经过加亮
author: 谇雨
layout: post
permalink: /jquery-table-change-color.html
views:
  - 306
categories:
  - 前端设计
tags:
  - 表格
  - 隔行变色
  - 鼠标经过
---
用过JavaScript的实现表格的隔行变色，鼠标经过加亮至少需要20行以上的代码，而是用jQuery只需要短短几行的代码。

td {padding:6px 11px;border-bottom:1px solid #95bce2;vertical-align:top;text-align:center;} 
td * {padding:6px 11px; } 
/*偶数行 tr 加上背景色*/
tr.even td {background:#ecf6fc;} 
/*这个将是鼠标高亮行的背景色*/   
tr.over td {background:#bcd4ec;}  

<pre class="lang:js decode:true " >$(document).ready(function(){
	$('.pm tr').mouseover(function(){
		$(this).addClass('over');}).mouseout(function(){$(this).removeClass('over');
	});
	$('.pm tr:even').addClass('even');
});

<!--more-->

  
值得一提的是上面代码中

<pre class="lang:js decode:true " >//本来应该这么写
$(".pm tr").mouseover(function(){     
          $(this).addClass("over");})  
$(".pm tr").mouseout(function(){     
          $(this).removeClass("over"); }) 
//但是我们写成了：
$('.pm tr').mouseover(function(){
		$(this).addClass('over');}).mouseout(function(){$(this).removeClass('over');
	});
//说明jQuery是链式的行为  