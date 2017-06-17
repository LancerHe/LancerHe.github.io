---
title: 在IE6中，line-height属性失效解决方案
author: 谇雨
layout: post
permalink: /ie6-line-heightl-ose-efficacy.html
views:
  - 123
categories:
  - 前端设计
tags:
  - IE6
  - line-height
---
<p style="text-indent: 2em;">
  经常发现在IE6中，line-height属性经常会失效，其实不然，当全文字的时候，line-height属性仍然有效，只有当文字和img、input、textarea、select、object等对象在同一个容器中的时候，line-height属性在这些非文字的对象中就会失效。
</p>

解决方法：  
在其中一个非文字的对象的样式中增加：  
margin: (容器的line-height -- 对象本身的高度)/2px 0;  
vertical-align:middle;