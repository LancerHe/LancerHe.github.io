---
title: MongoDB 学习笔记一
author: 谇雨
layout: post
permalink: /mongodb-study-note-1.html
categories:
  - NoSql
tags:
  - mongodb
  - 学习笔记
---

    #查看数据库
    show dbs;
    #查看当前数据库
    db;

    #查看集合/表         
    show collections;
    show tables; #为了兼容mysql习惯

    #给user集合插入一条JSON数据
    db.user.insert({name: 'user1', age: 25});

    #查询数据
    db.user.find();

    #删除集合数据
    db.user.remove();

    # 把"name"为"user2"修改为"user200"
    db.user.update({name:'user2'}, {$set: {name:'user200'}});

    #把"name"为"user200"的"sex"修改为"male"如果不存在，则增加
    db.user.update({name:'user200'}, {$set:{sex:'male'}});