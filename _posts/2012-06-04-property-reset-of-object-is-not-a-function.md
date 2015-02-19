---
title: 'Uncaught TypeError: Property &#8216;reset&#8217; of object # is not a function'
author: 谇雨
layout: post
permalink: /property-reset-of-object-is-not-a-function.html
views:
  - 1225
categories:
  - 前端设计
tags:
  - form
  - not a function
  - reset
  - typeerror
---
今天碰到一个非常特殊的问题，由于ajaxform提交后需要对当前form进行reset，但是在reset的时候一直报错：

> Uncaught TypeError: Property &#8216;reset&#8217; of object # is not a function

同样的两个form做这样的reset处理，只有一个会报错。

百般无奈，google了下，果然有猫腻。

来源于：<a href="http://x1989.com/a/476.html" target="_blank">http://x1989.com/a/476.html</a>

如果一个表单元素内部含有name属性为reset的表单元素，那么当你用js提交该表单，即：  
form1.reset();  
会出现报错：

> Uncaught TypeError: Property &#8216;reset&#8217; of object # is not a function

同理表单中有name属性为submit的表单元素，则form1.submit();也会报错

据说在国外网站上找到了吊爆的解决方案，暂时没有测试过：

> document.createElement(&#8216;form&#8217;).submit.call(form1);