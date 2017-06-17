---
title: jQuery插件添加回调方法
author: 谇雨
layout: post
permalink: /jquery_plugin_adding_callback_function.html
views:
  - 702
categories:
  - jQuery
tags:
  - jquery
  - 回调函数
  - 插件
---
在jQuery插件开发中，我们需要在执行某个动作的情况后促发一些回调函数，如hover, click, change等，或者当插件初始化完成后执行一个简单的回调函数。

<pre class="lang:js decode:true " title="My Plugin">$.fn.myPlugin = function( setting ) {

  var defaults = {
    color: 'red',
    size : '12px',
    initFn : null //当插件初始化完成后执行的回调函数， 默认为null
  }

  //默认值继承
  var config = $.extend( defaults, setting || {});

  // 引用回调函数
  if (typeof config.initFn == 'function') { // 确保类型为函数类型
		config.initFn.call(this); // 执行回调函数
  }

};</pre>

在页面中调用

<pre class="lang:js decode:true " title="实例化">$('#icon').myPlugin({
	color  : 'yellow',
	initFn : function () {
		alert('init successfully');
	}
})
</pre>