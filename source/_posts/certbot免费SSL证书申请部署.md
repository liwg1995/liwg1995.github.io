---
title: certbot免费SSL证书申请部署
date: 2017-10-18 11:19:53
tags: certbot,ssl
categories: "技术"
copyright: true
---
### 安装

```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install python-certbot-nginx
```
### 部署

<!--more-->

```
$ certbot  certonly  --cert-name  xxxx  -d  xxxx
```
> 第一个`xxx`是证书名，第二个`xxx`是为了给哪个证书申请的域名
 ### 更新
 > 申请的域名一般都是90天的，所以我们需要更新
 
```
$ certbot renew
```

---
> 参考：[certbot for nginx](https://certbot.eff.org/#ubuntuxenial-nginx)
> 以上是基于ubuntu 16.04的linux系统，更多的请参考：[certbot官网](https://certbot.eff.org)
