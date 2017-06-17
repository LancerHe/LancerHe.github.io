---
title: PHP多语言系统开发方式
author: 谇雨
layout: post
permalink: /php-muti-language-development.html
views:
  - 312
categories:
  - PHP
tags:
  - php
  - 多语言
  - 语言包
---
<p style="text-indent:2em">
  PHP多语言制作的目录一般为了支持多语言并可同时浏览不同语言版本的网页。也就是说为了方便用户本地化，可以让用户自己选择本地浏览的语言环境。
</p>

<p style="text-indent:2em">
  当用户选择某种语言时，我们如何显示该语种版本的网站？
</p>

<p style="text-indent:2em">
  对于每种语言都写一个版本，这种土方法没什么意义，不仅开发是低效率，带来的维护更不便利。所以可以采用语言包的形式：当用户浏览时则可根据POST或GET传递过来的语言参数，require相应的语言包，最后把网页内所有的文字、图片等都设置成可替换成语言包中的信息输出到浏览器上。倘若有使用框架或者模板引擎，原理也是一样。
</p>

<!--more-->

举个例子，如下是我在实际的一个项目中应用多语言技术的结构。  
项目文件结构如下：

admin———-后台管理文件夹

config———-项目中基本配置文件

class———-项目中包含所有的类

css———-项目中包含CSS

template———-模板文件

js———-js脚本

images———-图片存放目录

lang———-语言包存放目录

upload———-上传的文件存放目录

index.php———首页

&#8230;&#8230;.. 

1. 如何初始化语言信息

假设我们使用浏览器GET方式获取语言信息，传递参数为 lang=zh-cn, $lang = $_GET[&#8216;lang&#8217;]

那么我们就包含lang/zh-cn文件夹下的语言包文件，语言包可能包含如下文件：

site.php, admin.php, msg.php, product.php &#8230;&#8230;..

site.php 中可能是如下形式：

<pre class="lang:php decode:true " >$site['contact_us'] = '联系我们';
$site['my_account'] = '我的账户';
$site['company_info'] = '公司简介';
.........</pre>

就可以将特定的语言包信息显示出用户。也可以让用户保存自己使用的语言。

2. 后台如何添加新的语言

从上面的代码看出来，我们添加新的语言只需要添加对应的文件夹到lang文件夹下即可。 假如我们有一个英文的语言包en-us，就可以直接传到land文件夹下面。

总结  
语系/地区码  
en-us 英语  
es-es 西班牙语  
fr-fr 法语  
es-us 西班牙语(美国)  
zh-cn 简体中文  
zh-tw 繁体中文  
da-dk 丹麦语  
nl-nl 荷兰语  
fi-fi 芬兰语  
de-de 德语  
it-it 意大利语  
ja-jp 日语  
ko-kr 朝鲜语  
nb-no 挪威语  
pt-br 葡萄牙语  
es-es 西班牙语  
sv-se 瑞典语 

PS:上面的语言包只能解决一些静态的语言问题，倘若是发布新的产品，新闻这样的动态信息，就不是语言包能解决的问题，而是需要数据库的帮忙。  
由于不同语言的原因，每种信息的发布得次数也是不同的，所以对数据库多语言一般采用的是多语言数据表，数据库表可以采用如下的命名方式：  
{dbprefix}product_{lang}  
这样在检索时也是比较方便的。