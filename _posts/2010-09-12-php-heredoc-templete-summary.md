---
title: php中heredoc模板引擎方法小结
author: 谇雨
excerpt: >
  Heredoc技术，也叫字符串输出技术，在正规的PHP文档中和技术书籍中一般没有详细讲述，只是提到了这是一种Perl风格的字符串输出技术。之前在研究PW模板中经常发现，但一直不明白原理，其实这就是一种大量字符串输出的技术，这种技术不仅能大量输出字符串，而且还能实现了界面与代码的准分离，phpwind模板就是巧妙了使用这种技术，一个典型的例子！
layout: post
permalink: /php-heredoc-templete-summary.html
views:
  - 393
categories:
  - PHP
tags:
  - EOT
  - heredoc
  - php
  - 模板引擎
---
Heredoc技术，也叫字符串输出技术，在正规的PHP文档中和技术书籍中一般没有详细讲述，只是提到了这是一种Perl风格的字符串输出技术。之前在研究PW模板中经常发现，但一直不明白原理，其实这就是一种大量字符串输出的技术，这种技术不仅能大量输出字符串，而且还能实现了界面与代码的准分离，phpwind模板就是巧妙了使用这种技术，一个典型的例子！  
举个简单的例子：

<pre class="lang:php decode:true " >echo EOT<<<;
<span id="name" class="use">My name is Tom</span>
EOT;</pre>

&nbsp;&nbsp;&nbsp;&nbsp;这样就输出一段span 可以看出里面照样可以使用双引号。也就是说在EOT<<<与EOT之间字符串可以按照HTML格式写，不需要转义，而且易于阅读。  
<!--more-->&nbsp;&nbsp;&nbsp;&nbsp;既然有这么方便的东西，不是就可以当作模板吗？看下下面这个模板页面name.html

<pre class="lang:xhtml decode:true " ><!--<?php
$name = 'Tom';
print <<<EOT
-->
<html> 
<head> 
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" /> 
<title>Untitled Document</title> 
</head> 
<body> 
Hello,$name! 
</body> 
</html>
<!--<?php
EOT;
?>--></pre>

&nbsp;&nbsp;&nbsp;&nbsp;如果采用php页面include &#8216;name.html';结果输出Hello,Tom；  
&nbsp;&nbsp;&nbsp;&nbsp;而如果直接访问HTML;输出Hello,说明PHP部分被注释了。  
  
&nbsp;&nbsp;&nbsp;&nbsp;但是使用heredoc要注意几点：  
  
&nbsp;&nbsp;&nbsp;&nbsp;1.以<<<EOT开始标记开始，开始标记后面不能有空格，以EOT;结束标记结束，结束标记必须顶头写，不能有缩进和空格，且在结束标记末尾要有分号。之前因为养成了良好的缩进情况，一直无法正常解析，百思不得其解。。开始标记和开始标记相同，比如常用大写的EOT、EOD、EOF来表示，但是不只限于那几个，只要保证开始标记和结束标记不在正文中出现即可。  
  
&nbsp;&nbsp;&nbsp;&nbsp;2.位于开始标记和结束标记之间的变量可以被正常解析，但是函数则不可以。在heredoc中，变量不需要用连接符.或,来拼接，如果是数组需使用{}：如&#8217;Hi&#8217;$name, {$arrSet[&#8216;name&#8217;]}。