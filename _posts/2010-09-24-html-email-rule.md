---
title: 编写HTML邮件规则
author: 谇雨
layout: post
permalink: /html-email-rule.html
views:
  - 260
categories:
  - 前端设计
tags:
  - email
  - HTML
  - 规则
  - 邮件
---
> 参考文章：http://ued.koubei.com/?p=239

<p style="text-indent:2em;">
  由于最近经常做email campaign用于发送邮件，发现很多css在接收邮件里是有限制的，通过对以前的各位达人总结的学习，解决了这个问题，把一些心得记录下来。
</p>

<p style="text-indent:2em;">
  通常情况下不只是做email campaign,如会员注册确认等很多情况都会遇到通过发邮件通知会员。而通过站方发给会员信件，往往纯文本不能满足需要，因此多数情况下我们就需要发送HMTL页面。而各个邮箱后面却有自己的过滤算法，在接受邮件的时候或多或少会对它们接收的HTML邮件在后台进行过滤，这些过滤算法也不能那么容易被我们知道的。所以我们只能通过发邮件的形式来推测过滤掉的信息。
</p>

<!--more-->

<p style="text-indent:2em;">
  通过广大高手的测试，可知道邮箱分为两类:
</p>

<p style="text-indent: 25px;">
  第一类包括gmail、hotmail、sohu，这类邮箱，邮件内容是被布局在整个邮箱页面中的某个div中.
</p>

<p style="text-indent: 25px;">
  第二，包括163、sina，这类邮箱，邮件内容被布局在独立的iframe
</p>

<p style="text-indent:2em;">
  熟悉HTML都知道iframe完全是个独立的容器页面，与父页面没有任何关系。而div却整个邮箱页面的一个部分。这么看来DIV比iframe要严格的多。但不管是哪类邮箱，我们的目标肯定是保证大部分邮箱都能尽可能完美接收我们的邮件。为此列出一些编写规则：
</p>

<p style="text-indent: 25px;">
  1.javascript脚本文件毫无疑问，别说是操作DOM对象的属性，基于邮件安全性考虑，是被严格过滤，直接出局，不要用了。
</p>

<p style="text-indent: 25px;">
  2.不仅JS能被过滤，甚至CSS也会有部分过滤，类似于float、position等成非正常内容流的style会被部分邮箱过滤。
</p>

<p style="text-indent: 25px;">
  3.既然不能使用float左右布局，那么就只好用Table+CSS。
</p>

<p style="text-indent: 25px;">
  4.所有CSS避免使用外联写法，也不要用class，一律使用内联写法，避免与外部的CSS冲突导致页面乱了套。
</p>

<p style="text-indent: 25px;">
  5.少用图片，背景图不用CSS的background，有可能被过滤，所以使用table的background，虽然功能不及CSS，不过总比没的用好。。
</p>

不使用margin作为间距，使用padding，因为margin有些邮箱不支持。

<p style="text-indent: 25px;">
  6.少使用图片，用多了可能被当作垃圾邮件，而且每个图片要设置宽高，以免图片不能显示，并且加入Alt属性。
</p>

<p style="text-indent: 25px;">
  7.不要是的邮件布局太紧凑，留点宽度，避免不同邮箱可能出现文字溢出导致布局错乱。
</p>

<p style="text-indent: 25px;">
  8.不使用Flash、Java、Javascript、frames、i-frames、ActiveX 以及 DHTML。
</p>