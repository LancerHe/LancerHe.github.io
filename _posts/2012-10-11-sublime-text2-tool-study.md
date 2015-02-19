---
title: Sublime Text 2开发工具用法心得
author: 谇雨
layout: post
permalink: /sublime-text2-tool-study.html
views:
  - 1053
categories:
  - Software
tags:
  - Sublime Text
  - Tool
---
[<img class="alignnone size-full wp-image-693" title="sublime2" src="http://www.crackedzone.com/wp-content/uploads/2012/10/sublime2.jpg" alt="" width="590" height="150" />][1]

今天看到一款神器级的开发工具，<a href="http://sublimetext.com/" target="_blank">sublime text2(点这官网下载)</a> ，这一两天试用中，对各种编辑辅助功能，语法高亮，快捷键，snippet，插件都有大致的了解，列下各个细节功能点吧：

**<!--more-->1. 项目文件夹浏览：** sublime不同于np++，它是基于项目管理，侧边栏可以导入正在开发的项目文件夹，(ctrl+k,ctrl+b)快捷键可以开关侧栏(ps: 这里逗号分隔的两个快捷键代表按下ctrl的同时先按K再按B，这种方式大大增加了快捷键的使用有效率),同时右键菜单里可以remove folder移除文件夹

  
[<img class="aligncenter size-full wp-image-625" title="1" src="http://www.crackedzone.com/wp-content/uploads/2012/10/1.jpg" alt="" width="335" height="346" />][2]

**2. 文件快速导航：** 这是sublime上面很好用的功能之一，ctrl+p可以调出窗口，菜单上的解释是goto anythings ，确实如其所言，调出窗口后，直接输入关键字，可以在已打开的项目文件夹中进行快速文件名导航，而且支持模糊搜索 ，对于不想一直鼠标点开文件夹的同学来说极为方便，并且在该窗口里加上:前缀即为行跳转(ctrl+G)，加上@(ctrl+R)前缀在html里是id关键字导航，css里是每条规则导航，js里则是每个function导航。  
[<img class="aligncenter size-full wp-image-626" title="2" src="http://www.crackedzone.com/wp-content/uploads/2012/10/2.jpg" alt="" width="487" height="280" />][3]

**3. 命令行模式** ctrl+\` 可以调出命令行模式，主要支持python语法等，没试用过只知 quit()可以退出 。。不过sublime的插件平台package controller可以用命令行安装，<a href="http://wbond.net/sublime_packages/package_control/installation" target="_blank">详见这里</a>

[<img class="aligncenter size-full wp-image-627" title="31" src="http://www.crackedzone.com/wp-content/uploads/2012/10/31.png" alt="" width="338" height="173" />][4]

4. ctrl+up, ctrl+down可以一行一行的滚动屏幕，pageUp pageDown一页一页翻动

5. ctrl+j是拼合两行，ctrl+shift+d是直接复制当前行，

6. ctrl+/ 单行注释， ctrl+shift+/块注释，sublime会自动根据语言选择注释方式

7. ctrl+L 选择当前行，ctrl+d 选择当前文字块，ctrl+shift+a 可以HTML标签内容，每按一次逐级往上， ctrl+shift+space可以选择js等语言里的块{内容}, ctrl+shift+F 是文件夹里查找，与普通编辑不同的地方是sublime允许添加多个文件夹进行查找(add directory)  
[<img class="aligncenter size-full wp-image-628" title="12" src="http://www.crackedzone.com/wp-content/uploads/2012/10/12.png" alt="" width="448" height="118" />][5]

8. 在 菜单View-syntax下可以将当前面面以指定的语言进行语法高亮，通常情况下sublime会自动进行判断选择

9. ctrl+F2可以进行标记，然后F2在标记之间切换，这里不爽的是无法进行鼠标添加标记

10. snippets 代码片段插件 ，在tools-snippets菜单下可以查看当前页面支持的snippets，可以在缩略词后加tab自动补全snippets， snippets也可以从安装插件进行扩展

11. Build编译，可以进行ruby等代码的编译，我安装了一个yui compressor后，就可以在css js页面里进行压缩，快捷键是ctrl+B, 会自动在当前目录下生成xx.min.js, xx.min.css等压缩文件

12. 在菜单Preferences 下面是各种配置文件，sublime与其他编辑器不同的是，他不提供可视化配置，软件的配置及快捷键配置全都是存在.xml文件里手动配置，Settings-default 是系统默认配置，最好不要改，要改动的东西存放 在Settings-User里，比如font-size等， Key-bindings是快捷键配置, color scheme是代码配色方案选择，sublime本身提供了很多很不错的配色方案

13. 在页面右下角，有快速设置tab转为spaces的菜单，可以指定页面的tab宽度，也可以将页面所有tab转为指定的spaces格数

接下来介绍一下sublime的亮点，插件机制：

sublime插件可以通过两种方式进行插件安装，一种直接下载所有源码放置到C:\Users\yourName\AppData\Roaming\Sublime Text 2\Packages下面，另一种则可以通过在线安装 package control的方式进行在线安装，  
package control安装见其[官网][6]，就是在控制台里运行如下一段代码，再重启即可：

[python]import urllib2,os; pf=&#8217;Package Control.sublime-package'; ipp=sublime.installed\_packages\_path(); os.makedirs(ipp) if not os.path.exists(ipp) else None; urllib2.install\_opener(urllib2.build\_opener(urllib2.ProxyHandler())); open(os.path.join(ipp,pf),&#8217;wb&#8217;).write(urllib2.urlopen(&#8216;http://sublime.wbond.net/&#8217;+pf.replace(&#8216; &#8216;,&#8217;%20&#8242;)).read()); print &#8216;Please restart Sublime Text to finish installation'[/python]

安装完即可在菜单preferences-package control下打开（这里有个快捷键ctrl+shift+p是编辑器操作的命令集合，可以直接根据关键字索引，省却菜单栏寻找的过程），在package control下 Install Package是安装插件，选择后会连网获取插件列表，disabled package 是禁用某插件，enabled package 启用插件，remove package 移除插件，list package列出插件列表。  
[<img class="aligncenter size-full wp-image-629" title="14" src="http://www.crackedzone.com/wp-content/uploads/2012/10/14.png" alt="" width="422" height="418" />][7]

**比如以下常用的插件**

**1. GBK Encoding Support**: sublime本身不支持GBK，根据国情，装下这个插件就可以支持GBK了，同时也提供GBK与UTF之间转换

**2. jquery** 提供jquery的一些snippets片段 ，方便直接调用

**4. prefixr** 提供CSS3属性的浏览器前缀自动填充像-webkit- -moz- -o- -m-, 快捷键：ctrl+alt+x

**5. yui compressor **这个大家都知道yui的压缩工具，可以压缩CSS JS，直接CTRL+B，即可（需要安装配置了jdk之后才可用）

**6. sublime v8** 该插件提供jshint 及 v8引擎的js解析器console，jshint是JS语法校验器，较严格， v8则跟chrome里控制台一样。

**7. zenCoding** 大名鼎鼎的zencoding就不用多解释， 调用方式两种，一种是ctrl+alt+enter, 一种是输入完标签直接tab。

**8. html5 **该插件提供许多 html5相关的标签等snippets，也非常实用。

**9. Alignment** 该插件可以试一段代码中所有的等号对齐。

 [1]: http://www.crackedzone.com/wp-content/uploads/2012/10/sublime2.jpg
 [2]: http://www.crackedzone.com/wp-content/uploads/2012/10/1.jpg
 [3]: http://www.crackedzone.com/wp-content/uploads/2012/10/2.jpg
 [4]: http://www.crackedzone.com/wp-content/uploads/2012/10/31.png
 [5]: http://www.crackedzone.com/wp-content/uploads/2012/10/12.png
 [6]: http://wbond.net/sublime_packages/package_control/installation
 [7]: http://www.crackedzone.com/wp-content/uploads/2012/10/14.png