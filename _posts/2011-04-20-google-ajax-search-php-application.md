---
title: Google Ajax Search PHP站内搜索应用
author: 谇雨
layout: post
permalink: /google-ajax-search-php-application.html
views:
  - 704
categories:
  - PHP
  - SEO
tags:
  - Ajax
  - google
  - php
  - search
---
原理：Google Ajax Search其实就是返回在google上输入关键字的结果，在自己站点上用上。 

首先在google上申请一个Api Key  
<a href="http://code.google.com/intl/zh-CN/apis/websearch/docs/" target="_blank">http://code.google.com/intl/zh-CN/apis/websearch/docs/</a> 

主要采用JS的方法加载 

<pre class="lang:xhtml decode:true " ><script type="text/javascript" src="https://www.google.com/jsapi?key=INSERT_YOUR_KEY"></script></pre>

然后通过search对象控制搜索，官方API文档写的很具体。

但是有时候并不是很好控制，因此官方开放了另一个与其他语言交互的API接口  
  
<a href="http://code.google.com/intl/zh-CN/apis/websearch/docs/#fonje" target="_blank">http://code.google.com/intl/zh-CN/apis/websearch/docs/#fonje</a> 

通过curl获取Get参数的回传值 

> curl -e http://www.my-ajax-site.com \  
> &#8216;https://ajax.googleapis.com/ajax/services/search/web?v=1.0&q=Paris%20Hilton&key=INSERT-YOUR-KEY&#8217;

上述URL中https://ajax.googleapis.com/ajax/services/search/web 这一段基本为固定的   
参数 v=1.0也基本是Google固定的；  
参数 q为关键词，需要urlencode；  
参数 key为申请的key，其实不用必填，若填写了一定要正确的key。这个key直接可以关联到google account，所以还是建议申请。  
更多参数请查阅：<a href="http://code.google.com/intl/zh-CN/apis/websearch/docs/reference.html#_fonje_args" target="_blank">ttp://code.google.com/intl/zh-CN/apis/websearch/docs/reference.html#_fonje_args</a>。 

我这里用到另外一个类HttpClient模拟获取Get值，原理其实很Curl一样。  
  
HttpClient类可参考我之前写的这篇文章：  
<http://crackedzone.com/blog/httpclient-post-request-return.html> 

<!--more-->

  
具体方法：

<pre class="lang:php decode:true " >require_once 'inc/class/HttpClient.php';	
  
  function getGoogleSearchJson($args, $inSite=false) {
  //$args为传给Google的参数
  //默认inSite不开启的话 就是Google搜索，否则搜索本站，当然可以指定目录如site:crackedzone.com/blog
  $site = ($inSite == true) ? "site:crackedzone.com " : '';
  
  $url = 'https://ajax.googleapis.com/ajax/services/search/web'; 
  if ( !array_key_exists('v', $args) ) $args['v'] = '1.0';
  $args['q'] = $site . $args['q'];
  $url .= '?' . http_build_query($args, '', '&');
  
  //通过HttpClient类中的静态方法直接获取Get的返回值，返回的结构是JSON，需要反序列化为数组对象。
  $json = HttpClient::quickGet($url);
  $json = json_decode($json);
  
  return $json;
  
  }
  
  //通过传递关键字页数等信息，将之前反序列化的对象存为我们指定的字符串。
  function getSearchFormat ($args, $inSite=false) {
  
  global $keyword, $page;
  
  $formattedresults = '';
  $json = getGoogleSearchJson($args, $inSite);
  //print_r($json);
  
  //Response Succeess 200
  if ($json->responseStatus == 200) {
  
  $jsonResults = $json->responseData->results;
  $jsonCursor  = $json->responseData->cursor;
  $jsonRow     = $jsonCursor->estimatedResultCount;
  
  if (count($jsonResults)) {
  //Resules Exists
  foreach($jsonResults as $searchResult) {
  if($searchResult->GsearchResultClass == 'GwebSearch') {
  $formattedresults .= 
'</p>
          <div class="searchresult">
						<div class="title"><a href="' . $searchResult->unescapedUrl . '">' . $searchResult->title . '</a></div>
						<div class="desc">' . $searchResult->content . '</div>
						<div class="url">' . $searchResult->visibleUrl . '</div>
					</div>';
				}
			}
			
			
			$formattedresults .= '<div class="searchpage">';
			foreach($jsonCursor->pages as $searchPage) {
				$class = $searchPage->label == $page ? 'class="current"' : NULL;
				$formattedresults .= "<a {$class} href='?keyword={$keyword}&page=".$searchPage->label."'\>".$searchPage->label.'</a>';
			}
			$formattedresults .= '</div>';
			
		} else {
			//Result Noting
			$formattedresults = '<p class="note">Your search - '.$keyword.' - did not match any documents.</p>';
		
		}
		
	}
	return $formattedresults;
	
}

//使用函数方式
$keyword = trim($_GET['keyword']);
$page = $_GET['page'] ? (int)$_GET['page'] : 1;
$page < 1 && $page = 1;

if(!$keyword) {
	//没有输入关键字的时候
	$content = '<p class="note">Please enter one or more search terms in the search box above.</p>';
  
} else {
	
	$content = getSearchFormat(array( 
		'q'      => urlencode($keyword), // search keywords
		'key'    => 'Your API Key', 
		'userip' => getIP(), //客户机IP
		'rsz'    => 8, //每页显示记录数  最高8条
		'start'  => ($page-1) * 8 //记录开始的下界。
	), true); 
}</pre>

OK 最后写一些CSS样式表