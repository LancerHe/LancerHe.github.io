---
title: Setting Up with AWS EC2
author: 谇雨
layout: post
permalink: /setting-up-with-aws-ec2.html
categories:
  - AWS
tags:
  - AWS
---

### Create Security Groups

Serivces > NETWORK & SECURITY > Security Groups **[Create Security Group]**

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-1.png)

Press **[Create]**

<!--more-->

### Create Key Pair

Serivces > NETWORK & SECURITY > Key Pairs **[Create Key Pair]**

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-2.png)

Save **pem file**.

### Create EC2 Instance

1. Serivces > INSTANCES > Instance **[Launch Instance]**
2. Search **"centos 6 hvm"**

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-3.png)

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-4.png)

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-5.png)

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-6.png)

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-7.png)

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-8.png)

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-9.png)

### Create Elastic IP

Serivces > NETWORK & SECURITY > Elastic IP **[Allocate New Address]**

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-10.png)

### Create AMI

Serivces > Instance

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-11.png)

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-12.png)

![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-ec2-13.png)