---
title: PHPwind7.5 插件开发常用变量和函数
author: 谇雨
layout: post
permalink: /phpwind7-5-hack-develop-fun.html
views:
  - 132
categories:
  - Phpwind
tags:
  - phpwind
  - 二次开发
  - 插件开发
---
常量和变量:

<pre class="lang:php decode:true " >R_P  //用于记录总论坛目录的绝对路径
  D_P  //用于分论坛目录的绝对路径
  H_R  //用于论坛使用插件目录的绝对路径  ./hack/bank/
  phpwind  //常用变量
  $manager //创始人数组
  $windid  //登陆用户用户名
  $winduid //登陆用户UID
  $groupid  //用户所在组ID 游客为'guest'
  $onlineip //访问IP地址
  $timestamp //当前时间戳
  $imgdir    //图片dir
  $attachdir //附件dir</pre>

<!--more-->

  
常用函数:

<pre class="lang:php decode:true " >//对所有变量进行过滤，论坛没定义的变量都不能在程序中运行 
function InitGP($keys,$method=null,$cvtype=1)

//提示信息
function Showmsg($msg_info,$dejump=0)

//输出模板
function PrintEot($template,$EXT='htm')

//输出页脚
function footer() 

//将字符串转换为UNIX时间戳 
function PwStrtoTime($time) 

//针对SQL语句的变量进行反斜线过滤，并两边添加单引号 
function pwEscape($var,$strip = true) 

//过滤数据每个元素值，并进行逗号合并
function pwImplode($array,$strip=true) 

//获取数据的分页连接串
function numofpage($count,$page,$numofpage,$url,$max=null)</pre>