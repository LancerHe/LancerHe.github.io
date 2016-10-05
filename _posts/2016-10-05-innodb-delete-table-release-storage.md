---
title: MySQL InnoDB删除数据后释放磁盘空间
author: 谇雨
layout: post
permalink: /innodb-delete-table-release-storage.html
categories:
  - MySQL
tags:
  - innodb
---

Innodb数据库对于已经删除的数据只是标记为删除，并不真正释放所占用的磁盘空间，这就导致InnoDB数据库文件不断增长。

如果在创建数据库的时候设置 innodb_file_per_table=1，这样 InnoDB 会对每个表创建一个数据文件，然后只需要运行OPTIMIZE TABLE 命令就可以释放所有已经删除的磁盘空间。  
运行 OPTIMIZE TABLE 表名后，虽然最后会报 Table does not support optimize, doing recreate + analyze instead，但其实已经成功了。

那么如果没有设置这个参数，又想释放空间，彻底释放这些已经删除的数据，需要把数据库导出，删除InnoDB数据库文件，然后再倒入。

下面是基本的步骤：

1. 使用mysqldump命令将InnoDB数据库导出
2. 停止MySQL
3. 删除所有InnoDB数据库文件和日志
4. 启动MySQL并自动重建InnoDB数据库文件和日志文件
5. 导入前面备份的数据库文件

具体DEMO:

    # 备份数据库：
    mysqldump -uroot -proot --quick --force --all-databases > mysqldump.sql
    # 停止数据库
    service mysqld stop
    # 删除这些大文件
    rm /usr/local/mysql/var/ibdata1
    rm /usr/local/mysql/var/ib_logfile*
    # 手动删除除Mysql之外所有数据库文件夹，然后启动数据库
    service mysqld start
    # 还原数据
    mysql -uroot -proot < mysqldump.sql