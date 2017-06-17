---
title: Composer使用内网私有仓库进行包管理
author: 谇雨
layout: post
permalink: /composer-private-repository.html
categories:
  - Composer
tags:
  - 私有仓库
---

由于长期使用Composer进行包依赖管理，许多公用的类库都会放在GitHub上，在多个项目之间引用依赖，更新版本时，减少了很多成本，但还是存在一些问题：

* 每次更新，本地需要跑过单元你测试，Commit并Push到Github，等待Composer更新后才生效；
* 公司级的模块没有办法放在Github上；

### 官方方案

庆幸的事，Composer 就提供私有仓库的形式：

	{
	    "repositories": [
	        {
	            "type": "vcs",
	            "url": "http://svn.example.org/projectA/",
	            "trunk-path": "Trunk",
	            "branches-path": "Branches",
	            "tags-path": "Tags"
	        }
	    ]
	}


具体参考：[https://getcomposer.org/doc/05-repositories.md](https://getcomposer.org/doc/05-repositories.md)

由于公司内使用的是 `Subverison`，使用如上方式导入私有仓库，实际上是通过 `svn checkout` 的形式将指定的 `tags` 检出，这时候`vendor`目录下将会出现 `.svn` 目录，导致主项目无法提交，甚至在切换`tags`时出现无法检出的情况。

在`Composer`项目`issue`中有许多人反映此问题，解决方案是通过 `svn export` 来代替 `svn checkout`，需要使用如下插件：  
[https://github.com/LinearSoft/composer-svn-export](https://github.com/LinearSoft/composer-svn-export)

### 新方案

该插件等于是需要自己搭建一个`packaglist`站点，其实仅需要一个`packages.json`文件，但是需要有效的`https`服务，否则将会出现如下错误：

	➜  www.my-project.com composer update
	You are running composer with xdebug enabled. This has a major impact on runtime performance. See https://getcomposer.org/xdebug
	Added SvnExport repo: NewPackage
	Loading composer repositories with package information
	
	                                                                                                                                             
	  [Composer\Downloader\TransportException]                                                                                                   
	  The "https://packagist.my-project.dev/packages.json" file could not be downloaded: SSL operation failed with code 1. OpenSSL Error messages:  
	  error:14090086:SSL routines:ssl3_get_server_certificate:certificate verify failed                                                          
	  Failed to enable crypto                                                                                                                    
	  failed to open stream: operation failed         


如果没有这样的`https`服务，可以选择在`gist`上建一个`packages.json`文件来达到效果：

	{
	    "packages": {
	        "tc/analyse": {
	            "0.9.2": {
	                "name": "tc/analyse",
	                "version": "0.9.2",
	                "source": {
	                    "type": "svn",
	                    "url": "https://svn.tc.dev/library/analyse",
	                    "reference": "/tags/0.9.2"
	                }
	            },
	            "0.9.3": {
	                "name": "tc/analyse",
	                "version": "0.9.3",
	                "source": {
	                    "type": "svn",
	                    "url": "https://svn.tc.dev/library/analyse",
	                    "reference": "/tags/0.9.3"
	                }
	            },
	            "dev-master": {
	                "name": "tc/analyse",
	                "version": "dev-master",
	                "source": {
	                    "type": "svn",
	                    "url": "https://svn.tc.dev/library/analyse",
	                    "reference": "/trunk"
	                }
	            }
	        }
	    }
	}

主项目中`composer.json`填充上该`Gist`地址

	{
	    "require" : {
	        "php" : ">=5.4.0",
	        "tc/analyse" : "0.9.3",
	        "linearsoft/composer-svn-export" : "^0.1.2"
	    },
	    "extra" : {
	        "svn-export-repositories" : [
	            {
	                "name" : "TC",
	                "type" : "composer",
	                "url" : "https://gist.githubusercontent.com/lancerhe/379eeee89fd0db8c0c4ca19c6bddas2f/raw/915a720635949c9d4013746845b952f88af358db"
	            }
	        ]
	    },
	    "minimum-stability" : "stable"
	}

运行 `composer update`

	➜  www.my-project.com composer update
	You are running composer with xdebug enabled. This has a major impact on runtime performance. See https://getcomposer.org/xdebug
	Added SvnExport repo: TC
	Loading composer repositories with package information
	Updating dependencies (including require-dev)
	  - Updating tapcash/analyse (0.9.2 => 0.9.3)
	    Exporting /tags/0.9.3
	
	Writing lock file
	Generating autoload files