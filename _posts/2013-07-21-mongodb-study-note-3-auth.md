---
title: 'MongoDB学习笔记三 -- 用户授权'
author: 谇雨
layout: post
permalink: /mongodb-study-note-3-auth.html
categories:
  - NoSql
tags:
  - mongodb
  - 学习笔记
  - 用户授权
---

    #选择admin数据库
    use admin;
    #创建一个用户名为root，密码为root的帐户；
    db.addUser("root", "root");

    #执行查看数据表，发现失败，需要登陆。
    show collections;

所以我们重新登录test数据库

    /usr/local/mongodb/bin/mongo -uroot -proot localhost:27017/test

发现还是失败，因为授权的是admin数据库，所以：

    /usr/local/mongodb/bin/mongo -uroot -proot localhost:27017/admin

    user test

创建一个用户名为test，密码为123的帐户；

    show dbs;

因为用户test只是test数据的用户，只有admin数据库的用户才是超级管理员，可以查看任何数据库

启动服务的时候请加上`--auth`的选项

    /usr/local/mongodb/bin/mongod --auth --dbpath=/usr/local/mongodb/data/ --logpath=/usr/local/mongodb/dblogs --fork

MongoDB才会进行认证