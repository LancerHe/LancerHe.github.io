---
title: PHP二维数组在排序的应用
author: 谇雨
layout: post
permalink: /php-two-dimensional-array-sort.html
views:
  - 432
categories:
  - PHP
tags:
  - php
  - 二维数组
  - 排序
---
最近弄一个二次开发的API接口，一直在找PHP是否可以直接对二维数组中的某个键名进行排序，平时PHP对一维数组的排序比较简单，但是当我们在读取数据库写入数组后，可能经过一系列复杂的操作，最后形成一个二维数组，那次是如果为了更好的浏览数据，就必须对这个二维数组按不同键名进行排序。  
查了下手册PHP本身有一个多维数组排序的函数：

> bool array_multisort ( array $arr1 [, mixed $arg [, mixed $&#8230; [, array $&#8230; ]]] )

下面是手册当中对于array_multisort函数的描述：

> array_multisort() 可以用来一次对多个数组进行排序，或者根据某一维或多维对多维数组进行排序。  
> 关联(string)键名保持不变，但数字键名会被重新索引。  
> 输入数组被当成一个表的列并以行来排序――这类似于 SQL 的 ORDER BY 子句的功能。第一个数组是要排序的主要数组。数组中的行(值)比较为相同的话就按照下一个输入数组中相应值的大小来排序，依此类推。 

<!--more-->

  
由于我们通常取出处理的二维数组是这样的：

$arr = array (
	1 => array('id' => 2, 'fid' => '3' , 'data' => 'aa' ),
	2 => array('id' => 3, 'fid' => '1' , 'data' => 'bb' ),
	3 => array('id' => 1, 'fid' => '2' , 'data' => 'bb' )
);

假如我们需要把这个数组按照fid排序，按照array_multisort函数的要求我们就要取出fid这一列，然后根据这一列的升序，来调整原来的数组也升序。

function multi_array_sort($multi_array,$sort_field,$sort_type = SORT_ASC){
	if (!is_array($multi_array)) return FALSE;
	foreach ($multi_array as $row){
		if(! is_array($row)) return FALSE;
		$arr_field[] = $row[$sort_field];
	}
	array_multisort($arr_field,$sort_type,$multi_array);
	return $multi_array;
}//看看结果
print_r(multi_array_sort($arr, 'fid',SORT_DESC));