---
title: 一些常用的CSS HACK
author: 谇雨
layout: post
permalink: /some-css-hack.html
views:
  - 51
categories:
  - PHP
tags:
  - CSS
  - Hack
---
<table width="300" border="0" cellspacing="0" cellpadding="0">
  <tr>
    <td>
      &nbsp;
    </td>
    
    <td>
      IE6
    </td>
    
    <td>
      IE7
    </td>
    
    <td>
      FF
    </td>
  </tr>
  
  <tr>
    <td>
      *
    </td>
    
    <td>
      √
    </td>
    
    <td>
      √
    </td>
    
    <td>
      ×
    </td>
  </tr>
  
  <tr>
    <td>
      !important
    </td>
    
    <td>
      ×
    </td>
    
    <td>
      √
    </td>
    
    <td>
      √
    </td>
  </tr>
  
  <tr>
    <td>
      下划线(_)
    </td>
    
    <td>
      √
    </td>
    
    <td>
      ×
    </td>
    
    <td>
      ×
    </td>
  </tr>
</table>

于是可以这样来区分IE6，IE7，firefox

background:orange !important;
*background:green;
_background:blue;

<!--more-->

  
仅IE7识别  
*+html {…}  
当面临需要只针对IE7做样式的时候就可以采用这个HACK。

IE6及IE6以下识别  
* html {…}  
这个地方要特别注意很多地主都写了是IE6的HACK其实IE5.x同样可以识别这个HACK。其它浏览器不识别。  
html >body select {……}  
这句与上一句的作用相同。  
用法：  
*html #box{…}