---
title: DW中的正则替换应用
author: 谇雨
layout: post
permalink: /dw-regex-replace-application.html
views:
  - 79
categories:
  - 站长日记
tags:
  - dw
  - regex
  - repalce
---
今天发现使用DW中的正则替换特别好用，于是正好发现网上有一篇实例教程写的很好，转自：

<a title="使用DW正则表达式批量替换实例" href="http://oa.yubooa.com/html/4588.html" target="_blank">http://oa.yubooa.com/html/4588.html</a>

网站优化过程中，经常会遇到很多要批量修改的东西，一个个修改，明显效率低下。本文是使用DW正则表达式批量替换的一个实例。

<pre class="lang:xhtml decode:true " ><!--想法：想把-->
<a  href="#" target="_blank">BBBBBBB</a>
<!--替换成 -->
<a  href="http://www.yourdomain.com/keyword.asp?=BBBBBBB" target="_blank">BBBBBBB</a>
</pre>

如果&#8221;BBBBBBB&#8221;一样的话，那么简单，如果内容不一样的话，就不能使用简单的查找替换功能了。那么就使用DW正则表达式批量替换吧。

要替换内容：

[<img class="aligncenter size-full wp-image-530" title="1" src="{{ site.url }}/uploads/2012/03/1.png" alt="" width="311" height="369" />][1]

替换要达到的效果：

[<img class="aligncenter size-full wp-image-530" title="1" src="{{ site.url }}/uploads/2012/03/1.png" alt="" width="311" height="369" />][1]

<!--more-->替换的正则表达式：

[<img class="aligncenter size-full wp-image-532" title="2" src="{{ site.url }}/uploads/2012/03/2.png" alt="" width="653" height="286" />][2]

说明： （.\S+） &#8212;-用来替换A标签中的锚文本 {S为大写，代表任何单个非空白字符。}.  
$1 则是引用A标签中的锚文本（.\S+）的参数。  
注意：如果是非数字用（.\S+）， 如果是数字则用d，也就是（.\d+）。

最终效果：

[<img class="aligncenter size-full wp-image-533" title="4" src="{{ site.url }}/uploads/2012/03/4.png" alt="" width="649" height="357" />][3]

下面提供DW正则表达式表说明:

<table width="98%" border="0" cellspacing="0" cellpadding="0">
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">字符</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">匹配</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">示例</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">^</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">输入或行的起始部分。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">^T 匹配&#8221;This good earth&#8221;中的&#8221;T&#8221;，但不匹配&#8221;Uncle Tom&#8217;s Cabin&#8221;中的&#8221;T&#8221;。</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">$</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">输入或行的结尾部分。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">h$ 匹配&#8221;teach&#8221;中的&#8221;h&#8221;，但是不匹配&#8221;teacher&#8221;中的&#8221;h&#8221;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">*</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">0 个或多个前置字符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">um* 匹配&#8221;rum&#8221;中的&#8221;um&#8221;、&#8221;yummy&#8221;中的&#8221;umm&#8221;以及&#8221;huge&#8221;中的&#8221;u&#8221;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">+</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">1 个或多个前置字符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">um+ 匹配&#8221;rum&#8221;中的&#8221;um&#8221;和&#8221;yummy&#8221;中的&#8221;umm&#8221;，但在&#8221;huge&#8221;中没有任何匹配项</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">?</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">前置字符最多出现一次（即，指示前置字符是可选的）。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">st?on 匹配&#8221;Johnson&#8221;中的&#8221;son&#8221;和&#8221;Johnston&#8221;中的&#8221;ston&#8221;，但在&#8221;Appleton&#8221;和&#8221;tension&#8221;中没有任何匹配项</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">.</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">除换行符外的任何单字符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">.an 匹配短语&#8221;bran muffins can be tasty&#8221;中的&#8221;ran&#8221;和&#8221;can&#8221;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">x|y</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">x 或 y。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">FF0000|0000FF 匹配 bgcolor=&#8221;#FF0000&#8243; 中的&#8221;FF0000&#8243;和 font color=&#8221;#0000FF&#8221; 中的&#8221;0000FF&#8221;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">{n}</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">恰好 n 个前置字符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">o{2} 匹配&#8221;loom&#8221;中的&#8221;oo&#8221;和&#8221;mooooo&#8221;中的前两个&#8221;o&#8221;，但在&#8221;money&#8221;中没有任何匹配项</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">{n,m}</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">至少 n 个、至多 m 个前置字符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">F{2,4} 匹配&#8221;#FF0000&#8243;中的&#8221;FF&#8221;和&#8221;#FFFFFF&#8221;中的前四个&#8221;F&#8221;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">[abc]</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">用括号括起来的字符中的任何一个字符。用连字符指定某一范围的字符（例如， [a-f] 等效于 [abcdef]）。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">[e-g] 匹配&#8221;bed&#8221;中的&#8221;e&#8221;、&#8221;folly&#8221;中的&#8221;f&#8221;和&#8221;guard&#8221;中的&#8221;g&#8221;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">[^abc]</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">未在括号中括起来的任何字符。用连字符指定某一范围的字符（例如，[^a-f] 等效于[^abcdef]）。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">[^aeiou] 最初匹配&#8221;orange&#8221;中&#8221;r&#8221;、&#8221;book&#8221;中的&#8221;b&#8221;和&#8221;eek！&#8221;中的&#8221;k&#8221;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\b</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">词边界（例如空格或回车符）。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">\bb 匹配&#8221;book&#8221;中的&#8221;b&#8221;，但在&#8221;goober&#8221;和&#8221;snob&#8221;中没有任何匹配项</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\B</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">词边界之外的任何内容。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">\Bb 匹配&#8221;goober&#8221;中的&#8221;b&#8221;，但在&#8221;book&#8221;中没有任何匹配项</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\d</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">任何数字字符。等效于 [0-9]。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">\d 匹配&#8221;C3PO&#8221;中的&#8221;3&#8243;和&#8221;apartment 2G&#8221;中的&#8221;2&#8243;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\D</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">任何非数字字符。等效于 [^0-9]。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">\D 匹配&#8221;900S&#8221;中的&#8221;S&#8221;和&#8221;Q45&#8243;中的&#8221;Q&#8221;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\f</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">换页符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;"> </span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\n</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">换行符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;"> </span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\r</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">回车符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;"> </span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\s</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">任何单个空白字符，包括空格、制表符、换页符或换行符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">\sbook 匹配&#8221;blue book&#8221;中的&#8221;book&#8221;，但在&#8221;notebook&#8221;中没有任何匹配项</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\S</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">任何单个非空白字符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">\Sbook 匹配&#8221;notebook&#8221;中的&#8221;book&#8221;，但在&#8221;blue book&#8221;中没有任何匹配项</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\t</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">制表符。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;"> </span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\w</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">任何字母数字字符，包括下划线。等效于 [A-Za-z0-9_]。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">b\w* 匹配&#8221;the barking dog&#8221;中的&#8221;barking&#8221;以及&#8221;the big black dog&#8221;中的&#8221;big&#8221;和&#8221;black&#8221;</span>
    </td>
  </tr>
  
  <tr>
    <td width="9%">
      <span style="font-family: 宋体;">\W</span>
    </td>
    
    <td width="31%">
      <span style="font-family: 宋体;">任何非字母数字字符。等效于 [^A-Za-z0-9_]。</span>
    </td>
    
    <td width="58%">
      <span style="font-family: 宋体;">\W 匹配&#8221;Jake&Mattie&#8221;中的&#8221;&&#8221;和&#8221;100%&#8221;中的&#8221;%&#8221;</span>
    </td>
  </tr>
</table>

 [1]: {{ site.url }}/uploads/2012/03/1.png
 [2]: {{ site.url }}/uploads/2012/03/2.png
 [3]: {{ site.url }}/uploads/2012/03/4.png