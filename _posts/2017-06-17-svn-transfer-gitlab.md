---
title: SVN 迁移到 GitLab
author: 谇雨
layout: post
permalink: /svn-transfer-gitlab.html
categories:
  - Git
tags:
  - svn
  - git
---

由于公司搬迁到原因，一直以来都和其他部门公用SVN，权限分配也是分管部门配置的，SVN上很多项目文件，各种branches，tags命名不规范化，产品运营也都混合使用，介于此情况下，考虑将我们有价值以及正在研发都项目都迁移到我们自己到Git服务上，综合考虑决定搭建一个GitLab。

部署GitLab本身很简单，官方有简单到安装命令步骤

    yum install curl policycoreutils openssh-server openssh-clients
    yum install postfix
    curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
    yum install gitlab-ce
    yum install lokkit
    lokkit -s http -s ssh

安装完先打开 /etc/gitlab/gitlab.rb 修改几个简单到配置，域名/备份路径/仓库路径(保证有充足到磁盘空间)

    external_url 'http://gitlab.mydomain.com'
    gitlab_rails['backup_path'] = "/home/git/backups"
    git_data_dirs({"default" => { "path" => "/home/git" }})

初始化配置

    gitlab-ctl reconfigure

重启GitLab服务

    gitlab-ctl restart

设置Crontab备份操作

      0  2  *  *  * /usr/bin/gitlab-rake gitlab:backup:create

之后访问平台WEB操作，重置管理员密码，创建用户，组，项目信息

一切就绪，我们需要开始导入原来SVN上需要到项目，这里需要操作时支持git svn命令

1. 建立users.txt：存储svn账号与gitlab上账号的关联性，如下：  
yonghe = yonghe<×××@163.com>  
lihe = lihe<×××@163.com>  

格式: svn用户名 = git用户名<git用户对应的邮箱帐号>

注意: svn里面有的账号必须要做关联，否则clone会失败。比如上面的user11找不到是哪个开发人员，也不知道它该对应哪个git账号，那就随便指定一个git账号就行了，这样做的目的其实就是将user11在svn里面的所有提交日志关联到yqdong的git账号下。转到git之后，原svn账号就无关紧要，各司其职了。
 
绑定 users.txt 的意义仅在于，将svn里面的提交者日志，注意是提交者，不是svn里面所有的用户信息都得关联，仅仅是往项目提交过代码的svn账号，将这些svn账号找出来，然后关联到现有的git账号，一旦转到git上之后，每个git用户就能看到自己过去在svn里面提交的日志，绕了这么大一圈，现在明白了吧？

可以通过以下命令快速创建 users.txt

    svn log -q | awk -F '|' '/^r/ {sub("^ ", "", $2); sub(" $", "", $2); print $2" = "$2" <"$2">"}' | sort -u > users.txt

2. 导出svn上到项目数据

    git svn clone https://svn.mydomain/amz/selenium-center/ --trunk="trunk" --tags="tags" --branches="branches" --authors-file=amz-selenium-center/users.txt --no-metadata selenium-center

3. 添加GitLab到Git源信息

    git remote add origin git@gitlab.mydomain.com:amz/selenium-server.git

4. 将trunk, branches等上传

   git push origin master

回到GitLab平台既可看到项目相关Commit信息

更多参考文档：[实际操作 Svn 迁移到 Git](https://segmentfault.com/a/1190000007039598){:target="_blank"}
