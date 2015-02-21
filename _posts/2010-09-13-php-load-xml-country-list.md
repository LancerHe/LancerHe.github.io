---
title: PHP读取XML国家列表
author: 谇雨
layout: post
permalink: /php-load-xml-country-list.html
views:
  - 206
categories:
  - PHP
tags:
  - php
  - XML
  - 列表
  - 国家
---
一般来说国家列表非常长的，在客户端一般不使用连接数据库来读取数据的国家列表。这不仅消耗内存而且让页面加载速度也变慢，为此把国家列表存在XML中是一个很好的选择。  
部分XML数据：data/countries.xml，列表太长，完整的放在附件中。

<pre class="lang:xhtml decode:true " ><?xml version="1.0" encoding="utf-8"?>
<countries>
	<region id="1" name="America">
		<country id="92" name="Uruguay" code="UY" />
		<country id="93" name="Venezuela" code="VE" />
	</region>
	<region id="2" name="Europe">
		<country id="169" name="Ukraine" code="UA" />
		<country id="232" name="United Arab Emirates" code="AE" />
		<country id="174" name="United Kingdom" code="UK" />
	</region>
	<region id="3" name="Asia">
		<country id="59" name="Turkmenistan" code="TM" />
		<country id="65" name="Yemen" code="YE" />
	</region>
	<region id="4" name="Africa">
		<country id="135" name="Nigeria" code="NG" />
		<country id="125" name="Rwanda" code="RW" />
		<country id="117" name="Zimbabwe" code="ZW" />
	</region>
		<country id="149" name="Papua New Guinea" code="PG" />
		<country id="152" name="Samoa" code="WS" />
		<country id="155" name="Tonga" code="TO" />
		<country id="156" name="Vanuatu" code="VU" />
	</region>
</countries></pre>

<!--more-->

  
PHP函数及调用方法：index.php

<pre class="lang:php decode:true " >function get_country_select($xml, $sele=0,$type='id'){
	if(!file_exists($xml)) return 'File not exists';
	
	$country_xml = simplexml_load_file($xml);	
	$list = '';
	foreach($country_xml->children() as $region){		
		$list .= "<optgroup label=\"---".$region['name']."---\">\n";
		foreach($region->children() as $country){
  		$country_id   = $country['id'];
  		$country_name = $country['name'];
  		$list.='<option value="';
			if($type == 'id'){
				$list .= $country_id.'"';
				if($sele == $country_id) $list.= ' selected';
			}else{     	
				$list.= $country_name.'"';
				if($sele == $country_name) $list.= ' selected';
			}
     	$list .= '>'.$country_name."</option>\n";
  	}
		$list .= "</optgroup>\n";
	}
	return $list;
}

$xml = './data/countries.xml';
$list = get_country_select($xml);
echo '<select id=country>';
echo $list;
echo '</select>';</pre>

为什么要把<select>写在函数外面呢？因为往往select需要id,name毕竟提交表单需要一些信息，个人并不喜欢函数带太多参数。  
国家列表完整XML下载：<a href= 'http://blog.crackedzone.com/wp-content/uploads/2010/09/countries.zip' rel='attachment wp-att-52'>countries.xml</a>