---
title: Windows上使用CSS合并打包工具css-combo
author: 谇雨
layout: post
permalink: /windows-css-combo-note.html
categories:
  - PHP
tags:
  - css-combo
  - nodejs
  - windows
  - 打包
---
首先介绍下css-combo的功能：把CSS模块化的文件，打包合并到一个CSS文件中。

为什么项目中可能需要使用这个工具，作者给出了详细的说明。  
[http://www.techcheng.com/study/css/introduce-css-combo.html](http://www.techcheng.com/study/css/introduce-css-combo.html){:target="_blank"}

ccs-combo的源文件托管地址：  
[https://github.com/daxingplay/css-combo](https://github.com/daxingplay/css-combo){:target="_blank"}

由于这个是用nodejs做的小工具，所以我们必须安装nodejs和npm模块管理操作扩展。

1. 下载Node.js在Windows平台安装文件  
[http://nodejs.org/download/](http://nodejs.org/download/){:target="_blank"}  
创建D:\nodejs目录，并将node.exe保存在这个目录中。并将&#8221;D:\nodejs&#8221;加入系统环境变量PATH中，便于在任意位置执行node应用。

2. 下载npm源代码：  
[http://nodejs.org/dist/npm/](http://nodejs.org/dist/npm/){:target="_blank"}  
将npm源代码解压到D:\npmjs目录中，在命令提示符窗口中执行下面的操作，完成npm的安装：

    D:\>cd npmjs
    node cli.js install npm -gf

3. 安装css-combo  

    npm install -g css-combo

4. 使用css-combo  
命令行下，可以先进入需要打包的文件所在目录，然后

    csscombo test.source.css test.combo.css

<!--more-->

#### 批量处理项目中需要打包的文件

1. 需要先建立一个txt文件，比如pack-list.txt，里面的结构如下，类似一个二维数组。
    
    D:\www\test\css\ source-1.css source-1.combo.css
    D:\www\test\css\ source-2.css source-2.combo.css

2. 建立一个bat，循环读出txt行内容，并直接执行csscombo命令。

    @echo off

    for /f  "tokens=1-3 delims= " %%i in (pack-list.txt) do (
        csscombo %%i%%j %%i%%k
    )

#### 给CSS右键添加一个Combo功能

1. 首先也是建立一个bat文件，用于执行csscombo时的自动判断文件给文件重命名时加入一个combo后缀。

    @echo off
    csscombo %1 -o %~n1.combo%~x1

2. 然后建立一个reg文件，用于提交注册表，保证CSS文件的shell有这个命令。

    Windows Registry Editor Version 5.00

    [HKEY_CLASSES_ROOT\CSSFile\shell\Combo CSS]
    @="Combo CSS"

    [HKEY_CLASSES_ROOT\CSSFile\shell\Combo CSS\Command]
    @="D:\\www\\tools\\csscombo.bat %1"