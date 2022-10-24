---
title: Html email标签Mailto标签的使用方法
author: 谇雨
layout: post
permalink: /html-email-mailto-detail.html
views:
  - 99
categories:
  - 站长日记
tags:
  - mailto
  - subject
---
之前一直以为mailto只能选择邮件地址，由于任务需要添加默认标题，才特地搜索了下，结果发现果然有这个功能。

以下转自：<http://www.360doc.com/content/09/0805/14/16915_4684377.shtml>

Html中**mailto标签**是一个非常实用的贴近用户体验的标签，大多情况下人们都在这样使用

<pre class="lang:default decode:true " ><a href="mailto:example@phplamp.com">example@phplamp.com</a>

这样，当我们点击了example@phplamp.com链接后浏览器会为我们自动选择本地的默认邮件软件，并将mailto标签后example@phplamp.com邮件地址放到邮件软件的发送地址中，少去了我们发送邮件时输入发送地址的烦扰，同时也可以避免输错邮件地址。

其实mailto标签有更实用的使用方法。比如：**加入邮件默认标题，抄送地址，暗送（密件抄送）地址，邮件内容….**

下面说一下详细的使用方法：

**1. 为邮件加入默认标题**

<pre class="lang:default decode:true " ><a href="mailto:example@phplamp.com?subject=邮件的默认标题">example@phplamp.com</a>

**2. 为邮件加入默认的抄送地址**

<pre class="lang:default decode:true " ><a href="mailto:example@phplamp.com?cc=example2@phplamp.com">example@phplamp.com</a>

**3. 为邮件加入默认暗送（密件抄送）地址**

<pre class="lang:default decode:true " ><a href="mailto:example@phplamp.com?bcc=example2@phplamp.com">example@phplamp.com</a>

**4. 为邮件加入默认的内容**

<pre class="lang:default decode:true " ><a href="mailto:example@phplamp.com?body=欢迎您来到phplamp博客站-http://www.phplamp.org">example@phplamp.com</a>

**5. 多个邮件地址**

<pre class="lang:default decode:true " ><a href="mailto:example@phplamp.com,example2@phplamp.com">example@phplamp.com</a>

**6. 来个综合型的：加入默认抄送地址，标题，内容**

<pre class="lang:default decode:true " ><a href="mailto:example@phplamp.com?cc=example2@phplamp.com&#038;subject=phplamp博客站&#038;body=欢迎您来到phplamp博客站-http://www.phplamp.org">example@phplamp.com</a>

由上例可以看出，假如有多个默认值需要添加的话，与邮件地址相连接的字符为&#8221;?&#8221;，默认值间相连接的符号为&#8221;&&#8221;。