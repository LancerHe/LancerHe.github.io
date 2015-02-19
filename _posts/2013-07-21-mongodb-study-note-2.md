---
title: MongoDB 学习笔记二
author: 谇雨
layout: post
permalink: /mongodb-study-note-2.html
views:
  - 26
categories:
  - NoSql
tags:
  - mongodb
  - 学习笔记
---
<pre class="lang:sh decode:true " >#save和insert方法类似，都是插入数据，但是save当主键相同的时候会自动更新
db.c1.save({_id: 1, name:'user7'});

#删除name为user1的数据
db.c1.remove({name:'user1'});

#批量插入数据
for(i=1; i&lt;20; i++) { db.c1.insert({name:'user'+i, age:i }) }

#查询结构只需要 name, age, _id(id是默认有的)
db.c1.find({name:'user29'}, {name:1, age:1})

#查询结构只需要 name, 不用_id
db.c1.find({name:'user29'}, {name:1, _id:0})

#查询年龄大于5的数据
db.c1.find( {age: {$gt:5}} );

#查询年龄小于5的数据
db.c1.find( {age: {$lt:5}} );

#查询年龄大于等于5的数据
db.c1.find( {age: {$gte:5}} );

#查询年龄小于等于5的数据
db.c1.find( {age: {$lte:5}} );

#计算集合数量
db.c1.find().count();
db.c1.count();

#年龄倒序，取三条
db.c1.find().sort({age:-1}).limit(3);

#跳过2个，再去5个，LIMIT 2,5                 
db.c1.find().sort({age:-1}).skip(2).limit(5);

#count(1)表示统计之前的条件，否则跳过之前的条件
db.c1.find().sort({age:-1}).skip(2).limit(5).count(1)

#查询数组中包含相关值
db.c2.insert({name:"user1", post:[1,2,3,4,5]});                
db.c2.find({post:{$all:[1,3]}});    #查询post包含1,3            
db.c2.find({post:{$all:[1,6]}});                                

#查询age字段不存在的数据
db.c2.find({age:{$exists:0}})

#查询age字段除以2余1的数据                   
db.c1.find({age:{$mod:[2,1]}}) </pre>