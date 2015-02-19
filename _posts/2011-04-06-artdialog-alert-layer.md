---
title: 'JavaScript对话框(弹出层) &#8211; artDialog类库'
author: 谇雨
layout: post
permalink: /artdialog-alert-layer.html
views:
  - 1893
categories:
  - 前端设计
tags:
  - artDialog
  - 弹出对话框
---
最近由于项目需要，特别需要弹出层的JS类库。网上搜了大半发现artDialog这个JS库，发现确实很不错，还是国人制作的。不得不佩服，更支持国产~

artDialog是一个轻巧且高度兼容的javascript对话框组件，可让你的网页交互拥有桌面软件般的用户体验。

演示地址：<a href="http://www.planeart.cn/demo/artDialog/" target="_blank">http://www.planeart.cn/demo/artDialog/</a>  
项目主页：<a href="http://www.planeart.cn/demo/artDialog/" target="_blank">http://code.google.com/p/artdialog/</a>

[<img class="aligncenter size-full wp-image-418" title="aero" src="http://crackedzone.com/blog/wp-content/uploads/2011/04/aero.png" alt="" width="383" height="224" />][1]  
<!--more-->

  
其他皮肤：

google chrome：

<p style="text-align: center;">
  <a href="http://crackedzone.com/blog/wp-content/uploads/2011/04/artDialog01.jpg"><img title="artDialog01" src="http://crackedzone.com/blog/wp-content/uploads/2011/04/artDialog01.jpg" alt="" width="322" height="184" /></a>
</p>

facebook：

<p style="text-align: center;">
  <a href="http://crackedzone.com/blog/wp-content/uploads/2011/04/artDialog02.jpg"><img title="artDialog02" src="http://crackedzone.com/blog/wp-content/uploads/2011/04/artDialog02.jpg" alt="" width="358" height="188" /></a>
</p>

### 能够自适应内容

artDialog的特殊布局结构能够原生的适应内容变化，你不必像以前一样去考虑消息内容大小，她能自己去适应内容。当然她仍然可以接受一个固定高宽的参数，她能够防止内容溢出或截断，同时也不会产生难看的滚动条，甚至能够自动处理文本对齐方式。

### 优雅灵活的接口

artDialog很用心的设计了她的接口，她拥有丰富的配置参数控制初始化状态，并且每次调用后还会返回一些实用的内部控制接口，你可以先用变量把它保存起来，需要的时候在其他地方自由控制对话框。

### 精心设计的UI机制

artDialog采用九宫格的XHTML结构，CSS类钩子比较丰富，可以定制类似桌面软件般精致的皮肤。她支持多皮肤共存，这些皮肤的背景图片在页面载入时就会被缓存好，调用时几乎感觉不到延时。

### 细致的用户体验

她支持Esc快捷键关闭；如果对话框内容有输入控件她将支持Ctrl+Enter提交；智能给按钮添加焦点；小对话框自动采用黄金比例垂直居中；超过指定面积大小的对话框拖动自动采用替身挪动；自动计算边界防止超出可操作范围……

### 跨浏览器兼容

兼容：IE6+、Firefox、Chrome、Safari、Opera，浏览器版本越高体验越好。并且IE6下也能支持现代浏览器的静止定位(fixed)，支持覆盖下拉控件。

### 与jQuery协作

artDialog所有功能是不需要其他库就可以使用的，但是如果页面引入了jQuery，artDialog会奉献自己成为它的插件，利用它增强自身功能。

 [1]: http://crackedzone.com/blog/wp-content/uploads/2011/04/aero.png