---
title: CentOS5.5找不到ifconfig等命令
author: 谇雨
layout: post
permalink: /centoos5_5-find-ifconfig.html
views:
  - 344
categories:
  - Linux
tags:
  - CentOS
  - ifconfig
  - PATH
---
<p style="text-indent: 2em;">
  安装好CentOS5.5，和往常安装Ubantu和Fedora差不多，只不过准备配置网络时，输入ifconfig时候居然出现无法使用ifconfig命令，很明显这和Window上的命令行中无法找到的原理一样，没有配置环境变量。
</p>

<p style="text-indent: 2em;">
  测试下结果echo $PATH，果然/sbin这个主要目录不在PATH。
</p>

<p style="text-indent: 2em;">
  由于才疏学浅，终于学会了用 su root，使用管理员权限 运行vim /etc/profile 在文件末加入以下语句
</p>

<pre class="lang:sh decode:true " >PATH=$PATH:/sbin
export PATH=$PATH:/sbin</pre>

<p style="text-indent: 2em;">
  花了很久时间终于学会了使用:wq保存退出,注销重新登录,输入ifconfig,OK~
</p>