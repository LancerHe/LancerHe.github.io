---
title: PHP中CKEditor和CKFinder使用心得
author: 谇雨
layout: post
permalink: /php-ckeditor-ckfinder-studynotes.html
views:
  - 5044
categories:
  - PHP
tags:
  - ckeditor
  - ckfinder
  - php
---
[<img class="alignnone size-full wp-image-700" title="Ckeditor Ckfinder" src="{{ site.url }}/uploads/2011/03/Ckeditor-Ckfinder.jpg" alt="" width="590" height="150" />][1]

最近由于工作需要，一次性需要把所有的编辑器改进成CKEditor，不过这玩意没有上传功能，只好找到它的姐妹产品CKFinder。网上一查，才知道这两东西合伙才有希望完成上传等诸多功能。

由于之前都用FCKeditor，突然使用CKEditor蛮不适应，不过FCK由于有漏洞，也不敢用了，才知道原来FCK是CK的前身。 几经抓狂，花了半天时间终于搞定了。总结了以下心得：

1、/ckeditor/config.js, 配置文件，如果不想写太多，可以直接写好默认配置（语言，菜单栏，宽度）,有需要可以百度config配置

<pre class="lang:js decode:true " >config.language = 'en';
config.skin = 'v2';
config.uiColor = '#AADC6E';
config.toolbar = 'Basic';
....</pre>

2、官方的demo大多都喜欢用js配置editor区域，习惯写php的我就嫌麻烦，只好看内置的php类。

<pre class="lang:php decode:true " >require_once ROOTPATH . "ckeditor/ckeditor.php";
$CKEditor = new CKEditor();
$CKEditor->returnOutput = true; //设置输出可用变量的情况
$CKEditor->basePath = '/ckeditor/';//设置路径
$contentarea = $CKEditor->editor("content", $rs['contents']); //生成一个以name为content的textarea

echo $contentarea;</pre>

<!--more-->

  
3、需要上传了 ，只好加入ckfinder。把ckfinder和ckeditor放在同级目录下。  
打开/ckfinder/config.php, 首先设置第一个函数CheckAuthentication()，这个函数需要按照自己的规则写，只要return true的情况才能允许上传文件到服务器的，当然不建议直接写return true，这将导致安全问题。可以采用session来处理比较方便。

<pre class="lang:php decode:true " >session_start();
function CheckAuthentication(){
    if(isset($_SESSION['UseEidtor']))
        return true;
    else
        return false;
}</pre>

4、上传文件位置：也在/ckfinder/config.php, 找到$baseUrl，之前一直想自己写一个方法用来定位路径，实在不好办，后来只好用sesssion，如果一个网站中，有需要上传到不同的位置，正好可以利用session定位。

<pre class="lang:php decode:true " >if (isset($_SESSION['UseEidtor'])) {

switch ($_SESSION['UseEidtor']) {
case 'Addr1':
$baseUrl = '/addr1/uploadfile/';
case 'Addr2':
$baseUrl = '/addr2/upfiles/';
}

} else {

$baseUrl = '/upfiles/';

}
</pre>

5、对于上传文件名，ckfinder会按照原有的名字命名，中文的情况下可能会乱码，所以建议使用日期重命名。打开/ckfinder/core/connector/php/php5/CommandHandler/FileUpload.php 找到< /p>

<pre class="lang:php decode:true " >$sUnsafeFileName = CKFinder_Connector_Utils_FileSystem::convertToFilesystemEncoding(CKFinder_Connector_Utils_Misc::mbBasename($uploadedFile['name']));
</pre>

后面加上

<pre class="lang:php decode:true " >$sExtension = CKFinder_Connector_Utils_FileSystem::getExtension($sUnsafeFileName);
$sUnsafeFileName=date('YmdHis').'.'.$sExtension;
</pre>

6、 最后就是使用ckfinder

<pre class="lang:php decode:true " >require_once ROOTPATH . "ckeditor/ckeditor.php";
require_once ROOTPATH . 'ckfinder/ckfinder.php' ;

$CKEditor = new CKEditor();
$CKEditor->returnOutput = true;
$CKEditor->basePath = '/ckeditor/';

CKFinder::SetupCKEditor($CKEditor, '/ckfinder/') ;//注意这里是相对路径，相对于根目录，不能用绝对路径

$contentarea = $CKEditor->editor("content", $rs['contents']);
</pre>

两者配合用起来还是挺不错的，更重要的原因是安全性高了很多。

 [1]: {{ site.url }}/uploads/2011/03/Ckeditor-Ckfinder.jpg