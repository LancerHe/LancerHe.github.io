---
title: IE7下position的z-index Bug解决方案
author: 谇雨
layout: post
permalink: /ie7_position_z-index_bug_solution.html
views:
  - 4353
categories:
  - 前端设计
tags:
  - bug
  - IE7
  - position
  - z-index
---
[<img class="alignnone size-full wp-image-698" title="IE7-position" src="http://www.crackedzone.com/wp-content/uploads/2011/04/IE7-position.jpg" alt="" width="590" height="150" />][1]

通常设置position后，通过z-index属性来设置div的层叠情况。

但是在IE7中，设置position后，z-index会失效。导致div的层叠出现问题。

具体效果可以看这个页面

<a href="http://therealcrisp.xs4all.nl/meuk/IE-zindexbug.html" target="_blank">http://therealcrisp.xs4all.nl/meuk/IE-zindexbug.html</a>

以下分别是IE7与IE8之间的差别：

<div style="height: 300px;">
  <img class="size-full wp-image-430 alignleft" title="ie7" src="http://crackedzone.com/blog/wp-content/uploads/2011/04/ie7.jpg" alt="" width="270" height="270" /><br /> <img class="size-full wp-image-431  alignleft" title="ff" src="http://crackedzone.com/blog/wp-content/uploads/2011/04/ff.gif" alt="" width="270" height="270" />
</div>

<!--more-->

请先认真看他们的HTML和CSS信息：

<pre class="lang:xhtml decode:true " title="HTML结构" >&lt;div id="container"&gt;
    &lt;div id="box1"&gt;This box should be on top&lt;/div&gt;
&lt;/div&gt;
&lt;div id="box2"&gt;This box should not be on top; IE however seems to create a new stacking context for positioned elements, even when the computed z-index of that element is 'auto'.&lt;/div&gt;
</pre>

<pre class="lang:css decode:true " >body { margin: 0; padding: 0; }
#container { position: relative; }
#box1 { position: absolute; top: 100px; left: 510px; width: 200px; height: 200px; background-color: yellow; z-index: 20; }
#box2 { position: absolute; top: 50px; left: 460px; width: 200px; height: 200px; background-color: lime; z-index: 10; }
#content { width: 420px; padding: 20px; }</pre>

正常理解是应该显示上图右边的那种情况，但是IE7上的确是一件很怪异的事情，查了许多资料终于找到了解决方案:  
<a href="http://brenelz.com/blog/squish-the-internet-explorer-z-index-bug/" target="_blank">http://brenelz.com/blog/squish-the-internet-explorer-z-index-bug/</a>

> In Internet Explorer positioned elements generate a new stacking context, starting with a z-index value of 0. Therefore z-index doesn’t work correctly. So we giving the parent element a higher z-index actual fixes the bug

以上告诉我们，IE设置了position的元素会生成一个新的stacking context，导致 z-index 为0，所以才失效了。所以我们需要在这个元素的父元素上设置一个更高的z-index值。

在上述的box1中的父元素container设置一个更大的z-index就能解决这个问题，如下：

<pre class="lang:css decode:true " >#container { position: relative; z-index:30;}</pre>

这一Bug差点搞残我。。。

 [1]: http://www.crackedzone.com/wp-content/uploads/2011/04/IE7-position.jpg