---
title: 文章系统中的Content html保存
author: 谇雨
layout: post
permalink: /php-article-content-html-save.html
views:
  - 64
categories:
  - PHP
tags:
  - article
  - content
  - HTML
---
最近做一个文章发布系统，突然发现我的PHP基础只是很薄弱，尤其是保存文章content部分的时候经常出现转义问题。

首先我们需要对POST和GET数据做自动转义过滤，那么我们需要判断服务器是否开启了自动转义，否则我们可能出现重复转义。

<pre class="lang:php decode:true " title="Magic Quotes Fix" >if ( ! get_magic_quotes_gpc() ) {
    function clean($data) {
        if (empty($data))
            return $data;
        else
            return is_array($data) ? array_map('clean', $data) : addslashes($data);
        }

    if (!empty($_GET)) {
        $_GET  = clean($_GET);
    }
    if (!empty($_POST)) {
        $_POST = clean($_POST);
    }

    $_COOKIE   = clean($_COOKIE);
    $_REQUEST  = clean($_REQUEST);
}
</pre>

情况1：  
通过上面的操作我们得到的数据就已经自动转义的

比如POST的text数据: this&#8221; is a test  
那么$_POST[&#8216;text&#8217;]: this\&#8221; is a test  
在入库时候后mysql值会自动去掉转义的符号，而显示 this&#8221; is a test  
入库的时候如果再做了一次mysql\_real\_escape_string 则会显示 this\&#8221; is a test

也就是说mysql\_real\_escape_string和addslashes只要选择一种使用  
据说mysql\_real\_escape_string会更加安全一些，但是这个函数必须在mysql连接以后才使用。所以大部分源码都采用addslashes。

情况2：  
如果这个时候我们需要保存的是文章关键词，或者文章标题。  
假若填写的POST使用的title为: 为什么1 + 2 > 2 &#8220;一种证明方案&#8221;  
这里出现了大于号和引号，那么我们就应该在入库的时候将大于号转化成> 双引号转化成&quot;这样读取以后才不会出现html代码。这时候我们就需要使用htmlspecailchars()这个函数。

<pre class="lang:php decode:true ">function escape($data, $quotestyle=ENT_COMPAT) {
    if (is_array($data)) {
        foreach ($data as $key => $value) {
            unset($data[$key]);

            $data[escape($key)] = escape($value);
        }
    } else {
        $data = htmlspecialchars($data, $quotestyle, 'UTF-8');
    }

    return $data;
}
</pre>

入库后则显示：为什么1 + 2 &gt  2 &quot;一种证明方案&quot;