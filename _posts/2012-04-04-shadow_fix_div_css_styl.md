---
title: 兼容阴影，固定DIV位置的CSS样式
author: 谇雨
layout: post
permalink: /shadow_fix_div_css_styl.html
views:
  - 188
categories:
  - 前端设计
tags:
  - CSS
  - fix
  - shadow
---
<pre class="lang:default decode:true " title="阴影CSS" >.shadow{
    width:250px;height:auto;border:#909090 1px solid;background:#fff;color:#333;
    filter:progid:DXImageTransform.Microsoft.Shadow(color=#909090,direction=120,strength=4);/*ie*/
    -moz-box-shadow: 2px 2px 10px #909090;/*firefox*/
    -webkit-box-shadow: 2px 2px 10px #909090;/*safari或chrome*/
    box-shadow:2px 2px 10px #909090;/*opera或ie9*/
/*
for ie:
direction 阴影角度 0°为从下往上 顺时针方向
strength 阴影段长度

-moz-box-shadow: 2px 2px 10px #909090;
-webkit-box-shadow: 2px 2px 10px #909090;
box-shadow:2px 2px 10px #909090;

第一个参数是x轴阴影段长度
第二个参数是y轴阴影段长度
第三个参数是往四周阴影段长度
第四个参数是阴影段颜色
*/
}</pre>

<pre class="lang:default decode:true " title="固定Div位置" >.fix{
    width:50px; position:fixed; background:black; 
    top:0; left:0;z-index:1000; 
    /*For IE6 */
    _position: absolute; 
    _top: expression(eval(document.compatMode &#038;&#038; document.compatMode == 'CSS1Compat') ?     
				documentElement.scrollTop :
				document.body.scrollTop);
    /*固定居中
    _top: expression(eval(document.compatMode &#038;&#038; document.compatMode == 'CSS1Compat') ?     
				documentElement.scrollTop + (document.documentElement.clientHeight-this.offsetHeight )/ 2 :
				document.body.scrollTop + (document.body.clientHeight - this.clientHeight )/ 2 );
    */
}
</pre>