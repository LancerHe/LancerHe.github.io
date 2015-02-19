---
title: Excel筛选两个表公共字段
author: 谇雨
layout: post
permalink: /excel-sifting-field.html
views:
  - 423
categories:
  - 站长日记
tags:
  - excel
  - vlookup
  - 筛选
---
<p style="text-indent: 2em;">
  今天一同事导出两个CSV，叫我帮忙弄个PHP脚本，从一个表中能筛选出另一个表的信息。刚想着这玩意上会刚写了个，结果说是上会那个功能呢是剔除信息的，这回功能是筛选的，虽然用脚本判断比较快但写起来导入导出也够费劲。发觉好像Excel本身就集成这种功能，于是百度了下终于被我发现了，一个叫Vlookup的函数可以直接在两个表中做比较，并返回信息。
</p>

<p style="text-indent: 2em;">
  声明以下非我原创，原文地址：<a href="http://www.utosee.com/post/vlookup.html">http://www.utosee.com/post/vlookup.html</a>
</p>

<p style="text-indent: 2em;">
  已知表 sheet1 中的数据如下，如何在数据表二 sheet2 中如下引用：当学号随机出现的时候，如何在B列显示其对应的物理成绩？
</p>

[<img src="http://crackedzone.com/blog/wp-content/uploads/2010/11/excel-01.jpg" alt="" title="excel-01" width="449" height="273" class="aligncenter size-full wp-image-253" />][1]

<p style="text-indent: 2em;">
  首先我们介绍下使用的函数 vlookup 的几个参数，vlookup是判断引用数据的函数，它总共有四个参数，依次是：
</p>

<!--more-->

  
1、判断的条件  
2、跟踪数据的区域  
3、返回第几列的数据  
4、是否精确匹配

根据问题的需求，这个公式应该是：=vlookup(a2,sheet1!$a$2:$f$100,6,true)

详细说明一下在此vlookup函数例子中各个参数的使用说明：

1、a2 是判断的掉条件，也就是说如果sheet2表中a列对应的数据和sheet1表中的数据相同方能引用；  
2、sheet1!$a$2:$f$100 是数据跟踪的区域，因为需要引用的数据在f列，所以跟踪的区域至少在f列，$是绝对引用（关于绝对引用可以参考这里）；  
3、6 这是返回什么数的列数，如上图的物理是第6列，所以应该是6，如果要求英语的数值，那么此处应该是5  
4、是否绝对引用，如果是就输入 true 如果是近似即可满足条件 那么输入false （近似值主要用于带小数点的财务、运算等）  
5、vlookup是垂直方向的判断，如果是水平方向的判断可使用Hlookup函数

结果如下图：

[<img src="http://crackedzone.com/blog/wp-content/uploads/2010/11/excel-02.jpg" alt="" title="excel-02" width="507" height="300" class="aligncenter size-full wp-image-254" />][2]<

 [1]: http://crackedzone.com/blog/wp-content/uploads/2010/11/excel-01.jpg
 [2]: http://crackedzone.com/blog/wp-content/uploads/2010/11/excel-02.jpg