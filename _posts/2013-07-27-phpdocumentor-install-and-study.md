---
title: phpDocumentor安装与使用
author: 谇雨
layout: post
permalink: /phpdocumentor-install-and-study.html
categories:
  - Linux
  - PHP
tags:
  - phpdocumentor
  - 安装
---

最近再总结一些PHP的规范, 正好项目也需要文档管理，翻到了phpDocumentor, 说起来现在有了phpDocumentor2了，用起来感觉还是很方便, 而且还带bootstrap样式，就安装到使用的过程写下来, 与大家分享。

这里是官方网站： [www.phpdoc.org](http://www.phpdoc.org/){:target="_blank"}

通过pear安装phpDocumentor2

    $ pear channel-discover pear.phpdoc.org
    $ pear install phpdoc/phpDocumentor-beta

因为phpDocumentor2是通过xml的方式来读写模板  
安装php-xml

    $ wget http://www.graphviz.org/graphviz-rhel.repo /etc/yum.repos.d/graphviz-rhel.repo
    $ yum install graphviz

使用phpDocumentor2  
phpdoc -d指定文件夹，-t输出文件夹，--template使用模板

    $ phpdoc -d /mnt/siteroot/im.crackedzone.com/ -t /mnt/siteroot/im.crackedzone.com/docs --template responsive</pre>

当然如果不习惯phpDocumentor2，也可以安装phpDocumentor1，[下载地址](http://pear.php.net/package/PhpDocumentor/download/){:target="_blank"}

    #1.5
    pear install PhpDocumentor-1.5.0a1
    #1.4.2
    pear install PhpDocumentor-1.4.2
    wget http://download.pear.php.net/package/PhpDocumentor-1.4.2.tgz

由于模板的关系1.5版本一下都是使用iso-8859-1的编码，所以为了防止乱码  
转化编码防止乱码

    find ./ -name '*.tpl' | xargs sed -i 's/iso-8859-1/utf-8/g'