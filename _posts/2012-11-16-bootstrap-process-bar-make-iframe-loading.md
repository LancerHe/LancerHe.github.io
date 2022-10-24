---
title: Bootstrap process bar 制作 iframe loading效果
author: 谇雨
layout: post
permalink: /bootstrap-process-bar-make-iframe-loading.html
views:
  - 1364
categories:
  - PHP
tags:
  - Bootstrap
  - iframe
  - Loading
---
[<img class="alignnone size-full wp-image-726" title="bootstrap-loading" src="{{ site.url }}/uploads/2012/11/bootstrap-loading.jpg" alt="" width="590" height="150" />][1]

最近特别喜欢Bootstrap，不仅仅因为他的简洁和漂亮，更因为他的可扩展和自定义组合效果让我着迷。  
今天正好在研究iframe loading，发现bootstrap process bar的样式非常适合做loading载入的样式。

先看看演示效果：<a target="_blank" href='{{ site.url }}/uploads/2012/11/demo.html'>点此查看</a>

<!--more-->

现在来讲讲过程，其实很简单。首先先定义HTML结构

<pre class="lang:xhtml decode:true " title="HTML部分" ><div id="layout-left">
    <ul id="left-nav" class="nav nav-list">
        <li class="active"><a href="http://www.crackedzone.com/"><i class="icon-home"></i>Home</a></li>
        <li class="nav-header">Category</li>
        <li><a href="http://www.crackedzone.com/category/crphp"><i class="icon-globe"></i>PHP</a></li>
        <li><a href="http://demo.crackedzone.com/"><i class="icon-star"></i>DEMO</a></li>
    </ul>
</div>
<div id="loading-frame">
    <div id="loading-wrap">
        <div class="progress progress-striped active">
            <div class="bar" style="width: 0%;"></div>
        </div>
    </div>
</div>
<div id="layout-right">
    <iframe id="content" frameborder="0" name="content" src="about:blank"></iframe>
</div>

loading-Frame为Loading所在整个区域，将用于覆盖在IFrame之上，形成遮罩效果。  
layout-left为左侧导航用于点击将内容显示在iframe内中。  
layout-right用于存放iframe容器。  
并且这几个Div用绝对定位比较合适。  
所以我们定义如下样式：

<pre class="lang:default decode:true " >#layout-left, #layout-right,#loading-frame,#loading-wrap {position: absolute;}
#layout-left{left: 0;width: 190px;z-index: 9;background-color: #ECF7FE;}
#layout-left a {font-size: 12px; font-family: "微软雅黑"}
#layout-right, #loading-frame {left: 197px;right: 0;z-index: 10; background: #FFF}
#loading-frame {background: #000; z-index: 11;}
#loading-wrap {width: 300px; left: 50%; top:40%; margin-left: -150px;}

定义样式要注意到 #loading-frame比#loading-wrap的z-index要高。  
接下来就是制作loading动画效果：

<pre class="lang:js decode:true " >var layout_left_width=190;
window.onresize = function() {
    var widths = document.body.scrollWidth-layout_left_width;
    var heights = document.documentElement.clientHeight-38;
    $("#content, #loading-frame").height(heights+25).width(widths-10);
    var heights = document.documentElement.clientHeight-38;
    $("#layout-left").height(heights+25);
}
window.onresize();

//上面此段用于保存各个区域的宽高能100%，也保证loading-frame和content的区域一样宽高。

//设置透明度遮罩层，该层大小和IFrame一样，用于遮在IFrame上，并隐藏掉。
$("#loading-frame").css('opacity', .8).hide();

var interval = 0;
//当用户点击左侧导航上的链接的时候开始出现loading效果
function loading_start() {
    if (interval) {
        clearInterval(interval);
    }
    $("#loading-frame .bar").css('width', 0);
    $("#loading-frame").show();
    var percent = 0;
    interval = setInterval(function(){
        percent += 10;
        if (percent == 100) {
            percent = 99;
        }
        if (percent < 100) {
            $("#loading-frame .bar").animate({'width': percent + '%'},'slow');
        }
    }, 300);
}

//当iframe已经完全载入后，隐藏loading-frame
function loading_complete() {
    $("#loading-frame .bar").animate({'width':'100%'},'fast');
    clearInterval(interval);
    setTimeout(function() {
        $("#loading-frame").fadeOut("slow");
    }, 200) 
}

给左侧的链接绑定上事件。
$("#left-nav a").bind('click', function(){
    $("#content").attr('src', $(this).attr("href"));
    loading_start();
    var iframe = document.getElementById("content");  
    if (iframe.attachEvent) {  
        iframe.attachEvent("onload", function() {  
            loading_complete();
        });  
    } else {  
        iframe.onload = function() {  
            loading_complete();
        };  
    }
    return false;
});

效果图如下：  
[<img src="{{ site.url }}/uploads/2012/11/bootstrap-loading-screen.jpg" alt="" title="bootstrap-loading-screen" width="1009" height="562" class="alignnone size-full wp-image-727" />][2]

 [1]: {{ site.url }}/uploads/2012/11/bootstrap-loading.jpg
 [2]: {{ site.url }}/uploads/2012/11/bootstrap-loading-screen.jpg