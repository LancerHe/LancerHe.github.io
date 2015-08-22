---
title: Setting Up with AWS Elastic Load Balancing
author: 谇雨
layout: post
permalink: /setting-up-with-aws-elb.html
categories:
  - AWS
tags:
  - AWS
---

### Create ELB

1.  EC2 Dashboard > LOAD BALANCING > Load Balancers **[Create Load Balancer]**
2.  Define Load Balancer  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-elb-1.png)
3. Assign Security Groups  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-elb-2.png)
4. Configure Security Settings  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-elb-3.png)
5. Configure Health Check  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-elb-4.png)
6. Add EC2 Instances(one or more Instances)  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-elb-5.png)
7. Add Tags
8. Review: Press **[Create]**

<!--more-->

### Check ELB Status

1. DNS Name: xxx-2019755801.us-west-2.elb.amazonaws.com
2. Status: 1 of 1 instances in service  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-elb-6.png)
3. Instance Status is InService (Not OutService)  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-elb-7.png)
4. Check Service  
![AWS]({{ site.url }}/uploads/2015/08/aws-sign-up-with-elb-8.png)