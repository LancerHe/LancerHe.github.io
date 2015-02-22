---
title: WordPress 迁移 Github Page
author: 谇雨
layout: post
permalink: /wordpress-to-github.html
categories:
  - Jekyll
tags:
  - jekyll
  - wordpress
---

### 原因

博客写了将近5年，说起来就前几年博文特别多，具体的应该是学习特别多，但是质量并不高，2014年开始基本很少，但质量还是不错，用了wordpress这么久最烦的就是维护它，不得不说基于数据库的HTML储存太糟糕了，写作体验也糟的不行。

随着 Github 兴起，也衍生出了 Markdown 这样最适合程序员的写作方式。公司内部的百度手册也使用 Markdown，因此部门的手册也被我改成 Markdown，另外自己在一年内基本也不用 Windows，大部分都基于 Linux 进行工作，更喜欢在 Bash 下干各种事情了。

看着 Wopus 每年 &yen;160 的空间支出，也没怎么用，顺便把迁移这事做了，因为 Github 是免费的！

<!--more-->

### 方案

Github Page + Jekyll + Disqus：

*   分类和标签导出
*   上传资源导出
*   博客的 URL 不变化
*   评论导入第三方Disqus

### 搭建 Github Page

在 Github 上创建项目，项目名必须是[your github username].github.io，访问的 URL 是  
[your github username].github.io  
如果不是这样的规范的话，页面会无法访问，你的项目只要在master分支即可，不需要特殊的分支。

注：如果是子项目需要在 gf-pages 分支，则访问的 URL 是  
http://[your github username].github.io/project

### 文章迁移 和 Jekyll

Jekyll 是基于 gem 进行安装的，所以先安装 Ruby 1.9.3 以上版本，[自动化脚本](https://github.com/LancerHe/awesome-setup/blob/master/install/ruby.sh){:target="_blank"}

Wordpress 导出插件 [wordpress-to-jekyll-exporter](https://github.com/benbalter/wordpress-to-jekyll-exporter){:target="_blank"}  
我这里使用 1.3 版本的进行导出，不知为何 2.0 版本的会出现PHP级别错误。

文章导出来后会包含一个_posts文件夹和你的静态文件，直接复制到你新建的 Jekyll 项目中即可。

_posts是 Jekyll 放文章的地方，导出的文章是.md后缀名的，Jekyll会动态编译成 html 

    jekyll serve -w -H 0.0.0.0 -P 5000

运行 jekyll serve 后项目目录下会出现 _site 目录，里面将是你的所有静态页面和资源，如果你有搭建 Nginx 可以直接将 root 指向 _site ，也通过浏览器 http://localhost:5000 访问。

### 评论迁移

原来使用的是 wordpress 自带的评论功能， wordpress 插件管理可以搜索到 Disqus 相关插件，只要在 Disqus 注册帐户就可以通过该插件将历史的所有评论导入 Disqus 。

![CNAME 设置]({{ site.url }}/uploads/2015/02/import-disqus.jpg)

### 批量处理

导出的文章，图片目录/静态URL可能不尽如人意，只需要通过 shell sed 命令批量处理下即可，既然不用 wordpress 剩余的都是一堆静态的 markdown 文档，爱怎么折腾怎么折腾吧。

### 域名重定向

github page 项目下放置一个 CNAME 文件里面是你的域名

![CNAME 设置]({{ site.url }}/uploads/2015/02/CNAME.jpg)

然后将 dnspod 中设置域名解析记录类型 CNAME ，记录值 [your github username].github.io

![dnspod 设置]({{ site.url }}/uploads/2015/02/github-page-dns.jpg)