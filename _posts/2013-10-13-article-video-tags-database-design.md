---
title: 文章/视频Tags数据库结构设计
author: 谇雨
layout: post
permalink: /article-video-tags-database-design.html
views:
  - 77
categories:
  - MySql
tags:
  - tag
  - 数据库
  - 设计表
---
标签表

<pre class="lang:tsql decode:true">CREATE TABLE `t_tags` (
  `t_id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '标签ID',
  `t_name` varchar(200) NOT NULL DEFAULT '' COMMENT '标签名称',
  `t_cname` varchar(200) NOT NULL DEFAULT '' COMMENT '标签别名',
  `t_count` int(11) unsigned NOT NULL DEFAULT '0' COMMENT '含有标签的对象集合数目',
  PRIMARY KEY (`t_id`),
  UNIQUE KEY `cname` (`t_cname`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;</pre>

关联表(多对多的映射)

<pre class="lang:tsql decode:true">CREATE TABLE `t_tag_relationships` (
  `tr_objectid` int(11) NOT NULL DEFAULT '0' COMMENT '含有tag的对象ID，如文章，视频，图片对应ID',
  `t_id` int(11) NOT NULL COMMENT '标签ID',
  PRIMARY KEY (`tr_objectid`,`t_id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;</pre>

查询指定文章下的tags信息

<pre class="lang:tsql decode:true">SELECT t_id, t_name, t_cname FROM t_tags WHERE t_id IN( 
    SELECT t_id FROM t_tag_relationships WHERE tr_objectid=1006
);
</pre>

查询指定文章相关tag信息的文章

<pre class="lang:tsql decode:true">SELECT tr_objectid FROM t_tag_relationships WHERE t_id IN( 
    SELECT t_id FROM t_tag_relationships WHERE tr_objectid=1006
) AND tr_objectid != 1006;
</pre>