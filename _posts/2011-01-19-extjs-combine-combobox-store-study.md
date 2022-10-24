---
title: ExtJs 结合ComboBox的Store学习心得
author: 谇雨
layout: post
permalink: /extjs-combine-combobox-store-study.html
views:
  - 3039
categories:
  - 前端设计
tags:
  - ComboBox
  - ExtJS
  - Store
---
在传统页面的设计中,使用select标记作为下拉列表框，但在ExtJs中 ComboBox并不是一个select，不得不说ExtJs把按钮改的面目全非，这看起来和select标记别无二致，只是更加的漂亮了，再看看ExtJs帮我生成的代码，会发现select扯不上任何关系了，取而代之的是N个div，复杂了N多，这便是美丽所付出的代价。

<p align='center'>
  <img src="http://crackedzone.com/blog/wp-content/uploads/2011/01/ext-combobox.gif" alt="" title="ext-combobox" width="500" height="282" class="aligncenter size-full wp-image-375" />
</p>

ComboBox组件却需要Ext.data来提供数据。于是Ext.data成了数据的来源，控件则负责显示数据。分工如此的明确，就像PHP中的MVC模式。  
<!--more-->

Ext.data中有三个比较复杂的类:**DataProxy, DataReader, Store**. 这三个类难以用语言来描述。暂时可以这么理解：  
**DataProxy** : 获取的数据(可以是数组，Json格式，xml格式)，来自本地或者远程。就像数据库中二维表的数据形式。  
**DataReader** : 定义数据项的逻辑结构，数据的类型等。就像数据库中二维表的字段定义。  
**Store** : 存储器，用于整合Proxy和Reader，相当于把两个东西整合起来。

Ext.data.DataProxy类并不常使用， 一般使用它的子类：  
**MemoryProxy **: 获取内存数据，可以是数组、json、xml  
**HttpProxy** : 使用HTTP协议通过ajax获取数据  
**ScriptTagProxy**: 和HttpProxy类似，但支持跨域获取数据。

如下我们定义一个城市的数据Proxy

<pre class="lang:js decode:true " title="定义Proxy" >var cityProxy = new Ext.data.MemoryProxy([
	[1,'福州市'],
	[2,'厦门市'],
	[3,'泉州市'],
	[4,'漳州市'],
	[5,'三明市']
]);

Ext.data.DataReader类,其实总是依靠DataProxy做事，主要定义Proxy的逻辑结构，如：逻辑名称:name,数据类型:type,列与数据源的索引映射：mapping等等  
data.DataReader类也有三个子类：**Ext.data.ArrayReader、Ext.data.JsonReader、Ext.data.XmlReader**。  
由于上面建立的数组形式的cityProxy，那么建立逻辑结果也需要使用ArrayReader

<pre class="lang:js decode:true " title="定义Reader" >var cityReader = new Ext.data.ArrayReader({},[
	{name: 'cid', type:'int', mapping:0},
	{name: 'cname', type:'string', mapping:1}
]);

接下来的Store就简单了

<pre class="lang:js decode:true " title="定义Store" >var cityStore = new Ext.data.Store({
	proxy: cityProxy,
	reader:cityReader,
	autoLoad:true //开启自动加载，一般情况下没有开启，属于延迟加载，也可以采用 cityStore.load()方法
});

我们用画张图来看看关系：  
<img src="http://crackedzone.com/blog/wp-content/uploads/2011/01/Ext-store-understand.gif" alt="" title="Ext-store-understand" width="265" height="196" class="aligncenter size-full wp-image-374" />  
可以看出Ext.data.Store的主要目的是在内存中建立一张数据表，在填充到形态千差万别的组件中，如：ComboBox和GridPanel。  
建立一个典型的下拉框：

<pre class="lang:js decode:true " title="建立Combo" >var cityCombo = new Ext.form.ComboBox({
	renderTo : Ext.getBody(),
	hiddenName  : 'city', //在ComboBox，中hiddenName才是真正的名字
	mode  :'local',   //默认是remote远程获取模式
	triggerAction:'all', 
	store : cityStore,   //选择我们刚刚的数据源
	displayField: 'cname',  //显示的字段
	valueField  : 'cid',    //真实的字段
	emptyText   : '请选择福建的城市',
});

再设置一个按钮通过ComboBox类中的两个方法：getValue()和getRawValue()，来查看下拉列表框的显示值和真实值：

<pre class="lang:js decode:true ">var btn = new Ext.Button({
	text : '列表框的值',
	renderTo : Ext.getBody(),
	handler : function() {
		Ext.Msg.alert('结构','实际值:' + cityCombo.getValue() + '; 显示值:' + cityCombo.getRawValue());
	}
});