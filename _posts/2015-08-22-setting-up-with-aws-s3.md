---
title: Setting Up with AWS S3
author: 谇雨
layout: post
permalink: /setting-up-with-aws-s3.html
categories:
  - AWS
tags:
  - AWS
---

### Create S3 Bucket
1. S3 Dashboard > **[Create Bucket]**
2. Create a Bucket - Select a Bucket Name and Region  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-s3-1.png)
3. Bucket: [cash-001]
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-s3-2.png)

<!--more-->

### Create AMI for S3 Bucket Users

1. AIM Dashboard > Users> [Create New Users]
2. Enter User Names: [cash-001]  
Generate an access key for each user: [Checked]
3. Press [Download Credentials]  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-s3-3.png)
4. Configure Permissions  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-s3-4.png)
5. Attach Policy  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-s3-5.png)
6. Policy Name : [AmazonS3FullAccess]  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-s3-6.png)

### Install s3cmd on CentOS 6

    yum install epel-release
    yum install s3cmd
    s3cmd --configure
    Access Key: **********************
    Secret Key: *****************************
    Default Region: us-west
    ...[default]
    Save settings? [y/N] Y

    s3cmd --help
    s3cmd ls

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-s3-7.png)