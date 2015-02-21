---
title: Jquery现实限制复选框多选数目
author: 谇雨
layout: post
permalink: /jquery-checkbox-limit-num.html
views:
  - 493
categories:
  - 前端设计
tags:
  - jquery
  - 复选框
  - 限制数目
---
最近在搞一个投票系统，要求可以发布后的投票可以实现至少选多少个，至多选多少个，和QQ空间的投票类似。其实用Jquery的遍历实现起来非常快。  
具体代码如下：

<pre class="lang:js decode:true " >var voteMax = 7
var voteMin = 3 

var errorMsgMax = "最多能选" + voteMax + "个";
var errorMsgMin = "最少能选" + voteMin + "个";

//定义好选择范围后，对name为option[]的复选框进行遍历，当点击的时候赋予事件
$("input:checkbox[name='option[]']").each(function(){
  $(this).click(function(){
  	//点击的时候 得到复选框已经勾选的数目
    var checkNum = $("input:checkbox[name='option[]']:checked").length;
    
    if (checkNum > voteMax ) {
      $(this).attr("checked","");
      alert(errorMsgMax);
    } else if(checkNum < voteMin ) {
      $(this).attr("checked","checked");
      alert(errorMsgMin);
    }
  });

});</pre>