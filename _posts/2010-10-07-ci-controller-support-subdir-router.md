---
title: CI让控制器可以支持多级子目录扩展Router类库
author: 谇雨
excerpt: >
  由于控制器下不能建立目录URL结构，比如我们类似需要一个后台文件夹，如：http://localhost/admin/home/这在原本的CI下是不支持的，所以必须将Router类进行改进，为了不该变system文件夹内容，我们可以把这个类扩展MY_Router。
layout: post
permalink: /ci-controller-support-subdir-router.html
views:
  - 1915
categories:
  - CodeIgniter
tags:
  - CI
  - Router
  - 控制器
  - 目录
---
<p style="text-indent: 2em;">
  转载自:<a href="http://codeigniter.org.cn/forums/thread-2849-1-1.html" target="_blank">http://codeigniter.org.cn/forums/thread-2849-1-1.html</a>
</p>

<p style="text-indent: 2em;">
  由于控制器下不能建立目录URL结构，比如我们类似需要一个后台文件夹，如：http://localhost/admin/home/这在原本的CI下是不支持的，所以必须将Router类进行改进，为了不该变system文件夹内容，我们可以把这个类扩展MY_Router。。
</p>

<!--more-->

<p style="text-indent: 2em;">
  把解压缩得到的 MY_Router.php 放到 system/application/libraries 目录下，就可以让 CI 的控制器支持多级子目录了这样，你就可以在 system/application/controllers 目录下放置更多级别的目录，访问的方式就是 index.php/目录1/目录2/目录3/控制器/方法/参数<br /> 请注意，你不需要 load，因为Router这个类是系统自动 load 的。
</p>

<p style="text-indent: 2em;">
  点击此处下载：<a href='http://crackedzone.com/blog/wp-content/uploads/2010/10/MY_Router.zip'>MY_Router</a>
</p>