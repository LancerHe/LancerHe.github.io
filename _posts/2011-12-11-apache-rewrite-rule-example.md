---
title: 如何在Apache环境下配置Rewrite规则
author: 谇雨
layout: post
permalink: /apache-rewrite-rule-example.html
views:
  - 63
categories:
  - Apache
tags:
  - Apache
  - rewrite
---
本文转载自：<http://www.cnblogs.com/xiaochaohuashengmi/archive/2010/05/24/1742815.html>

URL 静态化是一个利于搜索引擎的设置，通过 URL 静态化，达到原来是动态的 PHP 页面转换为静态化的 HTML 页面，当然，这里的静态化是一种假静态，目的只是提高搜索引擎的搜索量，Comsenz 旗下的产品 Discuz!、SupeSite/X-Space、ECShop、SupeV、UCHome 等都支持此功能。当然这个功能还需要服务器环境的支持，下面介绍一下如何在 Apache 服务器下配置 URL 静态化的 Rewrite 规则。

当然这里分两种情况，一种是独立主机用户，这部分用户拥有对主机的管理权限，因此配置起来比较方便一些。（注：这里就以 Discuz!6.1.0 的 Rewrite 规则为例，稍后在后面会列举出其他产品的 Rewrite 规则。

首先确定您使用的 Apache 版本，及是否加载了 mod_Rewrite 模块。  
Apache 1.x 的用户请检查 conf/httpd.conf 中是否存在如下两段代码：

<pre class="lang:apache decode:true">LoadModule Rewrite_module libexec/mod_Rewrite.so
AddModule mod_Rewrite.c</pre>

Apache 2.x 的用户请检查 conf/httpd.conf 中是否存在如下一段代码：

LoadModule Rewrite\_module modules/mod\_Rewrite.so

如果存在，那么在配置文件（通常就是 conf/httpd.conf）中加入如下代码。此时请务必注意，如果网站使用通过虚拟主机来定义，请务必加到虚拟主机配置，即 <VirtualHost> 中去，如果加在虚拟主机配置外部将可能无法使用，改好后将 Apache 重启。

<pre class="lang:apache decode:true"><IfModule mod_Rewrite.c>
RewriteEngine On
RewriteRule ^(.*)/archiver/((fid|tid)-[\w\-]+\.html)$ $1/archiver/index.php?$2
RewriteRule ^(.*)/forum-([0-9]+)-([0-9]+)\.html$ $1/forumdisplay.php?fid=$2&page=$3
RewriteRule ^(.*)/thread-([0-9]+)-([0-9]+)-([0-9]+)\.html$ $1/viewthread.php?tid=$2&extra=page\%3D$4&page=$3
RewriteRule ^(.*)/space-(username|uid)-(.+)\.html$ $1/space.php?$2=$3
RewriteRule ^(.*)/tag-(.+)\.html$ $1/tag.php?name=$2
</IfModule></pre>

如果没有安装 mod_Rewrite，您可以重新编译 Apache，并在原有 configure 的内容中加入 --enable-Rewrite=shared，然后再在 Apache 配置文件中加入上述代码即可。

另外一种用户是 Apache 虚拟主机用户。  
<!--more-->

  
在开始以下设置之前，请首先咨询您的空间服务商，空间是否支持 Rewrite 以及是否支持对站点目录中 .htaccess 的文件解析，否则即便按照下面的方法设置好了，也无法使用。  
检查论坛所在目录中是否存在 .htaccess 文件，如果不存在，请手工建立此文件。Win32 系统下，无法直接建立 .htaccess 文件，您可以从其他系统中拷贝一份，或者在 Discuz.net 技术支持栏目中下载此文件。编辑并修改 .htaccess 文件，添加以下内容：

<pre class="lang:apache decode:true"># 将 RewriteEngine 模式打开
RewriteEngine On
# 修改以下语句中的 /discuz 为你的论坛目录地址，如果程序放在根目录中，请将 /discuz 修改为 /
RewriteBase /discuz
# Rewrite 系统规则请勿修改
RewriteRule ^archiver/((fid|tid)-[\w\-]+\.html)$ archiver/index.php?$1
RewriteRule ^forum-([0-9]+)-([0-9]+)\.html$ forumdisplay.php?fid=$1&page=$2
RewriteRule ^thread-([0-9]+)-([0-9]+)-([0-9]+)\.html$ viewthread.php?tid=$1&extra=page\%3D$3&page=$2
RewriteRule ^space-(username|uid)-(.+)\.html$ space.php?$1=$2
RewriteRule ^tag-(.+)\.html$ tag.php?name=$1</pre>

添加内容时，请遵照上面的提示，修改论坛所在的路径，然后保存。将 .htaccess 文件上传到论坛所在的目录中。然后进入论坛系统设置的搜索引擎优化，根据需要开启 URL 静态化功能。

下面简单介绍一些其他产品的 Rewrite 规则。

**SupeSite/X-Space6.0 UC规则**

Apache 独立主机用户：

<pre class="lang:apache decode:true"><IfModule mod_Rewrite.c>
RewriteEngine On
###Rewrite 系统规则请勿修改
RewriteRule ^/([0-9]+)/spacelist(.+)$ /index.php?uid/$1/action/spacelist/type$2 [L]
RewriteRule ^/([0-9]+)/viewspace(.+)$ /index.php?uid/$1/action/viewspace/itemid$2 [L]
RewriteRule ^/([0-9]+)/viewbbs(.+)$ /index.php?uid/$1/action/viewbbs/tid$2 [L]
RewriteRule ^/([0-9]+)/(.*)$ /index.php?uid/$1/$2 [L]
RewriteRule ^/([0-9]+)$ /index.php?uid/$1 [L]
RewriteRule ^/action(.+)$ /index.php?action$1 [L]
RewriteRule ^/category(.+)$ /index.php?action/category/catid$1 [L]
RewriteRule ^/viewnews(.+)$ /index.php?action/viewnews/itemid$1 [L]
RewriteRule ^/viewthread(.+)$ /index.php?action/viewthread/tid$1 [L]
RewriteRule ^/mygroup(.+)$ /index.php?action/mygroup/gid$1 [L]
</IfModule></pre>

Apache 虚拟主机用户：

<div>
  <pre class="lang:apache decode:true ">### 将 RewriteEngine 模式打开
RewriteEngine On
### 修改以下语句中的 /SupeSite 修改为你的SupeSite目录地址，如果程序放在根目录中，请将 /SupeSite 修改为 /
RewriteBase /
### Rewrite 系统规则请勿修改
RewriteRule ^([0-9]+)/spacelist(.+)$ index.php?uid/$1/action/spacelist/type$2 [L]
RewriteRule ^([0-9]+)/viewspace(.+)$ index.php?uid/$1/action/viewspace/itemid$2 [L]
RewriteRule ^([0-9]+)/viewbbs(.+)$ index.php?uid/$1/action/viewbbs/tid$2 [L]
RewriteRule ^([0-9]+)/(.*)$ index.php?uid/$1/$2 [L]
RewriteRule ^([0-9]+)$ index.php?uid/$1 [L]
RewriteRule ^action(.+)$ index.php?action$1 [L]
RewriteRule ^category(.+)$ index.php?action/category/catid$1 [L]
RewriteRule ^viewnews(.+)$ index.php?action/viewnews/itemid$1 [L]
RewriteRule ^viewthread(.+)$ index.php?action/viewthread/tid$1 [L]
RewriteRule ^mygroup(.+)$ index.php?action/mygroup/gid$1 [L]</pre>
  
  <p>
    <strong>ECShop2.6.0 规则</strong>
  </p>
  
  <p>
    Apache 独立主机用户：
  </p>
  
  <pre class="lang:apache decode:true "><IfModule mod_Rewrite.c>
RewriteEngine On
RewriteRule ^(.*)/index.html$ $1/index.php
RewriteRule ^(.*)/category$ $1/index.php [L]
RewriteRule ^(.*)/feed-c([0-9]+).xml$ $1/feed.php?cat=$2 [L]
RewriteRule ^(.*)/feed-b([0-9]+).xml$ $1/feed.php?brand=$2 [L]
RewriteRule ^(.*)/feed.xml$ $1/feed.php
RewriteRule ^(.*)/category-([0-9]+)-b([0-9]+)-min([0-9]+)-max([0-9]+)-attr([^-]*)-([0-9]+)-(.+)-([a-zA-Z]+)(.*)\.html$ $1/category.php?id=$2&brand=$3&price_min=$4&price_max=$5&filter_attr=$6&page=$7&sort=$8&order=$9
RewriteRule ^(.*)/category-([0-9]+)-b([0-9]+)-min([0-9]+)-max([0-9]+)-attr([^-]*)\.html$ $1/category.php?id=$2&brand=$3&price_min=$4&price_max=$5&filter_attr=$6
RewriteRule ^(.*)/category-([0-9]+)-b([0-9]+)-([0-9]+)-(.+)-([a-zA-Z]+)(.*)\.html$ $1/category.php?id=$2&brand=$3&page=$4&sort=$5&order=$6
RewriteRule ^(.*)/category-([0-9]+)-b([0-9]+)-([0-9]+)(.*)\.html$ $1/category.php?id=$2&brand=$3&page=$4
RewriteRule ^(.*)/category-([0-9]+)-b([0-9]+)(.*)\.html$ $1/category.php?id=$2&brand=$3
RewriteRule ^(.*)/category-([0-9]+)(.*)\.html$ $1/category.php?id=$2
RewriteRule ^(.*)/goods-([0-9]+)(.*)\.html$ $1/goods.php?id=$2
RewriteRule ^(.*)/article_cat-([0-9]+)-([0-9]+)-(.+)-([a-zA-Z]+)(.*)\.html$ $1/article_cat.php?id=$2&page=$3&sort=$4&order=$5
RewriteRule ^(.*)/article_cat-([0-9]+)-([0-9]+)(.*)\.html$ $1/article_cat.php?id=$2&page=$3
RewriteRule ^(.*)/article_cat-([0-9]+)(.*)\.html$ $1/article_cat.php?id=$2
RewriteRule ^(.*)/article-([0-9]+)(.*)\.html$ $1/article.php?id=$2
RewriteRule ^(.*)/brand-([0-9]+)-c([0-9]+)-([0-9]+)-(.+)-([a-zA-Z]+)\.html $1/brand.php?id=$2&cat=$3&page=$4&sort=$5&order=$6
RewriteRule ^(.*)/brand-([0-9]+)-c([0-9]+)-([0-9]+)(.*)\.html $1/brand.php?id=$2&cat=$3&page=$4
RewriteRule ^(.*)/brand-([0-9]+)-c([0-9]+)(.*)\.html $1/brand.php?id=$2&cat=$3
RewriteRule ^(.*)/brand-([0-9]+)(.*)\.html $1/brand.php?id=$2
RewriteRule ^(.*)/tag-(.*)\.html $1/search.php?keywords=$2
RewriteRule ^(.*)/snatch-([0-9])\.html$ $1/snatch.php?id=$2
RewriteRule ^(.*)/group_buy-([0-9])\.html$ $1/group_buy.php?act=view&id=$2
</IfModule></pre>
  
  <p>
    Apache 虚拟主机用户：
  </p>
  
  <pre class="lang:apache decode:true ">RewriteEngine On
#RewriteBase /
# direct one-word access
RewriteRule ^index\.html$ index\.php [L]
RewriteRule ^category$ index\.php [L]
# access any object by its numeric identifier
RewriteRule ^feed-c([0-9]+)\.xml$ feed\.php\?cat=$1 [L]
RewriteRule ^feed-b([0-9]+)\.xml$ feed\.php\?brand=$1 [L]
RewriteRule ^feed\.xml$ feed\.php [L]
RewriteRule ^category-([0-9]+)-b([0-9]+)-min([0-9]+)-max([0-9]+)-attr([^-]*)-([0-9]+)-(.+)-([a-zA-Z]+)(.*)\.html$ category\.php\?id=$1&brand=$2&price_min=$3&price_max=$4&filter_attr=$5&page=$6&sort=$7&order=$8 [QSA,L]
RewriteRule ^category-([0-9]+)-b([0-9]+)-min([0-9]+)-max([0-9]+)-attr([^-]*)(.*)\.html$ category\.php\?id=$1&brand=$2&price_min=$3&price_max=$4&filter_attr=$5 [QSA,L]
RewriteRule ^category-([0-9]+)-b([0-9]+)-([0-9]+)-(.+)-([a-zA-Z]+)(.*)\.html$ category\.php\?id=$1&brand=$2&page=$3&sort=$4&order=$5 [QSA,L]
RewriteRule ^category-([0-9]+)-b([0-9]+)-([0-9]+)(.*)\.html$ category\.php\?id=$1&brand=$2&page=$3 [QSA,L]
RewriteRule ^category-([0-9]+)-b([0-9]+)(.*)\.html$ category\.php\?id=$1&brand=$2 [QSA,L]
RewriteRule ^category-([0-9]+)(.*)\.html$ category\.php\?id=$1 [QSA,L]
RewriteRule ^goods-([0-9]+)(.*)\.html$ goods\.php\?id=$1 [QSA,L]
RewriteRule ^article_cat-([0-9]+)-([0-9]+)-(.+)-([a-zA-Z]+)(.*)\.html$ article_cat\.php\?id=$1&page=$2&sort=$3&order=$4 [QSA,L]
RewriteRule ^article_cat-([0-9]+)-([0-9]+)(.*)\.html$ article_cat\.php\?id=$1&page=$2 [QSA,L]
RewriteRule ^article_cat-([0-9]+)(.*)\.html$ article_cat\.php\?id=$1 [QSA,L]
RewriteRule ^article-([0-9]+)(.*)\.html$ article\.php\?id=$1 [QSA,L]
RewriteRule ^brand-([0-9]+)-c([0-9]+)-([0-9]+)-(.+)-([a-zA-Z]+)\.html brand\.php\?id=$1&cat=$2&page=$3&sort=$4&order=$5 [QSA,L]
RewriteRule ^brand-([0-9]+)-c([0-9]+)-([0-9]+)(.*)\.html brand\.php\?id=$1&cat=$2&page=$3 [QSA,L]
RewriteRule ^brand-([0-9]+)-c([0-9]+)(.*)\.html brand\.php\?id=$1&cat=$2 [QSA,L]
RewriteRule ^brand-([0-9]+)(.*)\.html brand\.php\?id=$1 [QSA,L]
RewriteRule ^tag-(.*)\.html search\.php\?keywords=$1 [QSA,L]
RewriteRule ^snatch-([0-9]+)\.html$ snatch\.php\?id=$1 [QSA,L]
RewriteRule ^group_buy-([0-9]+)\.html$ group_buy\.php\?act=view&id=$1 [QSA,L]
RewriteRule ^auction-([0-9]+)\.html$ auction\.php\?act=view&id=$1 [QSA,L]</pre>
  
  <p>
    <strong>SupeV1.0beta 规则</strong>
  </p>
  
  <p>
    Apache 独立主机用户：
  </p>
  
  <pre class="lang:apache decode:true "><IfModule mod_Rewrite.c>
RewriteEngine On
RewriteRule ^(.*)/ivideo(-tv-([0-9]+))?(-ti-([0-9]+))?(-tc-([0-9]+))?(-page-([0-9]+))?\.html$ $1/ivideo.php?tv=$3&ti=$5&tc=$7&page=$9
RewriteRule ^(.*)/ispecial(-tv-([0-9]+))?(-ti-([0-9]+))?(-tc-([0-9]+))?(-page-([0-9]+))?\.html$ $1/ispecial.php?tv=$3&ti=$5&tc=$7&page=$9
RewriteRule ^(.*)/icategory\.html$ $1/icategory.php
RewriteRule ^(.*)/category-cid-([0-9]+)(-tag-([^-]*))?(-timelimit-([0-9]+))?(-orderlimit-([0-9]+))?(-page-([0-9]+))?\.html$ $1/category.php?cid=$2&tag=$4&timelimit=$6&orderlimit=$8&page=$10
RewriteRule ^(.*)/vspace-(mid|username)-(.+)\.html$ $1/vspace.php?$2=$3
RewriteRule ^(.*)/video-(vid|ivid)-(.+)\.html$ $1/video.php?$2=$3
RewriteRule ^(.*)/special-spid-([0-9]+)\.html$ $1/special.php?spid=$2
</IfModule></pre>
  
  <p>
    Apache 虚拟主机用户：
  </p>
  
  <pre class="lang:apache decode:true "># 将 RewriteEngine 模式打开
RewriteEngine On
# 修改以下语句中的 /SupeV 为你的播客目录地址，如果程序放在根目录中，请将 /SupeV 修改为 /
RewriteBase /SupeV
# Rewrite 系统规则请勿修改 RewriteRule
RewriteRule ^ivideo(-tv-([0-9]+))?(-ti-([0-9]+))?(-tc-([0-9]+))?(-page-([0-9]+))?\.html$ ivideo.php?tv=$2&ti=$4&tc=$6&page=$8
RewriteRule ^ispecial(-tv-([0-9]+))?(-ti-([0-9]+))?(-tc-([0-9]+))?(-page-([0-9]+))?\.html$ ispecial.php?tv=$2&ti=$4&tc=$6&page=$8
RewriteRule ^icategory\.html$ icategory.php
RewriteRule ^category-cid-([0-9]+)(-tag-([^-]*))?(-timelimit-([0-9]+))?(-orderlimit-([0-9]+))?(-page-([0-9]+))?\.html$ category.php?cid=$1&tag=$3&timelimit=$5&orderlimit=$7&page=$9
RewriteRule ^vspace-(mid|username)-(.+)\.html$ vspace.php?$1=$2
RewriteRule ^video-(vid|ivid)-(.+)\.html$ video.php?$1=$2
RewriteRule ^special-spid-([0-9]+)\.html$ special.php?spid=$1</pre>
  
  <p>
    <strong>UCHome1.0 规则</strong>
  </p>
  
  <p>
    Apache 独立主机用户：
  </p>
  
  <pre class="lang:apache decode:true ">RewriteEngine On
RewriteRule ^/(space|network)\-(.+)\.html$ /$1.php?Rewrite=$2 [L]
RewriteRule ^/(space|network)\.html$ /$1.php [L]
RewriteRule ^/([0-9]+)$ /space.php?uid=$1 [L]</pre>
  
  <p>
    Apache 虚拟主机用户：
  </p>
  
  <pre class="lang:apache decode:true ">RewriteEngine On
RewriteBase /
RewriteRule ^(space|network)\-(.+)\.html$ $1.php?Rewrite=$2 [L]
RewriteRule ^(space|network)\.html$ $1.php [L]
RewriteRule ^([0-9]+)$ space.php?uid=$1 [L]</pre>
  
  <p>
    当然，在我们每个发布的程序包中，都有关于此版本程序对应的 Rewrite 规则，大家可以在那里找到相对应的 Rewrite 规则。
  </p>
  
  <p>
    <strong>UCHome2.0 规则<br /> </strong>
  </p>
  
  <p>
    Apache 独立主机用户：
  </p>
  
  <p>
    # 修改以下语句中的 /uchome 修改为你的uchome目录地址，如果程序放在根目录中，请将 /uchome 修改为 /
  </p>
  
  <pre class="lang:apache decode:true ">RewriteRule ^/(space|network)-(.+)\.html$ /$1\.php\?rewrite=$2 [L]
RewriteRule ^/(space|network)\.html$ /$1\.php [L]
RewriteRule ^/([0-9]+)$ /space\.php\?uid=$1 [L]</pre>
  
  <p>
    Apache 虚拟主机用户：
  </p>
  
  <pre class="lang:apache decode:true "># 将 RewriteEngine 模式打开
RewriteEngine On
RewriteBase /03uchome
RewriteRule ^(space|network)\-(.+)\.html$ $1.php?rewrite=$2 [L]
RewriteRule ^(space|network)\.html$ $1.php [L]
RewriteRule ^([0-9]+)$ space.php?uid=$1 [L]</pre>
  
  <p>
    <strong>最后注意一点：<br /> </strong><strong><br /> </strong>查找到httpd.conf文件,如果AllowOverride为None的话..请一定把None都改成all.这样你写.htaccess这样的文件才会起到作用..
  </p>
</div>