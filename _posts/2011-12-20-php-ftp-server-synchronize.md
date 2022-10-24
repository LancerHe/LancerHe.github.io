---
title: 利用PHP登陆FTP实现服务器资源同步
author: 谇雨
layout: post
permalink: /php-ftp-server-synchronize.html
views:
  - 408
categories:
  - PHP
tags:
  - ftp
  - php
  - 同步
---
由于经常操作国外服务器，深刻的感受是速度比较低，有的时候可以说非常低。

最近需要两台服务器做下载数据分流。但是面临着一个问题：同时去上传两个资源到两台服务器明显是一件比较麻烦的事，而且速度很慢，花时间又多。所以想了一个办法，将资源上传到其中一台服务器，然后直接从另一台服务器登入这台服务器下载资源并拷贝到同样的目录下。

于是乎找了很多方法，其实最快的就是用linux上的shell方式，直接get文件就可以同步数据，但是可能服务的一些问题，并没有办法这么顺利，不过在php中的确找到了FTP相关的函数

> resource ftp_connect ( string $host [, int $port [, int $timeout ]] )

> bool ftp\_login ( resource $ftp\_stream , string $username , string $password )

> bool ftp\_get ( resource $ftp\_stream , string $local\_file , string $remote\_file , int $mode [, int $resumepos ] )

利用这三个函数足以实现同步资源。因此我设置了两个函数。<!--more-->

//FTP连接
    function loginFtp() {
        // set up basic connection
        $ftp_server = 'serverName Or Ip';
        $ftp_user  = 'username';
        $ftp_pass  = 'password';

        // connect ftp
        $ftp_conn = ftp_connect($ftp_sever);

        // login with username and password
        $login_result = ftp_login($ftp_conn, $ftp_user, $ftp_pass);

        // check connection
        if ( !$ftp_conn || ! $login_result) {
            die("FTP connection has failed!");
        }

        return $ftp_conn;
    }

    //同步文件
    function synchronize($server_file, $ftpconn) {
        $local_file  = ltrim($server_file, '/');
        $local_file  = str_replace('pub/', '', $local_file);

        //目录不存在 重新创建
        $folder = strrpos($local_file, '/');
        if ($folder !== FALSE) {
            $local_folder = substr($local_file, 0, $folder) . '/';
        }

        if ( ! is_dir($local_folder) ) {
            mkdir($local_folder, 0775, TRUE);
        }

        // download the file
        $rs = ftp_get($ftpconn, $local_file, $server_file, FTP_BINARY);

        if ( $rs ) {
            if ( ! file_exists($local_file) )
                echo "Successfully written to {$local_file}.<br />";
            else
                echo "Successfully rewritten to {$local_file}.<br />";
        } else {
            echo "There was a problem on {$local_file}.<br />";
        }
    }

    //实现同步

    $conn = loginFtp();
    $file = '/pub/files/msnlite.exe';
    synchronize($file, $conn);

    // close the FTP stream
    ftp_close($conn);

