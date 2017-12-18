---
title: ubuntu16.04安装openldap以及phpldapadmin
date: 2017-10-18 17:49:13
tags: ldap,phpldapadmin
categories: "技术"
copyright: true
---
## openldap
### 安装openldap

```
$ sudo apt-get update
$ sudo apt-get install slapd ldap-utils
```
<!--more-->

> 安装过程中会提示一些信息，自己输入一些`dc`，其他的询问`yes` or `no`的信息的时候，直接回车默认
### 重新配置sldap  

```
$ sudo dpkg-reconfigure slapd
```
> 期间会让重新输入密码，dc之类的信息，填写就成，其余的直接点回车
### 查询是否安装成功

```
$ ldapsearch -x
```
> 有信息输出就证明安装成功
- /etc/ldap/下的ldap.conf配置文件:

```
#
# LDAP Defaults
#

# See ldap.conf(5) for details
# This file should be world readable but not world writable.

BASE    dc=olei,dc=me
URI     ldap://127.0.0.1
#ldap://ldap-master.i1995.cn:666

#SIZELIMIT      12
#TIMELIMIT      15
#DEREF          never

# TLS certificates (needed for GnuTLS)
TLS_CACERT      /etc/ssl/certs/ca-certificates.crt
```
## phpldapadmin
### 安装phpldapadmin

```
$ sudo apt-get install phpldapadmin
```
### 配置文件
- /etc/phpldapadmin/config.php
1. 需要将这个给去掉注释，并修改为`true`：

```
$config->custom->appearance['hide_template_warning'] = true;
```
2. 修改下面的信息：

```
$servers->setValue('server','host','xx.xx.xx.xx');
/* The port your LDAP server listens on (no quotes). 389 is standard. */
// $servers->setValue('server','port',389);

/* Array of base DNs of your LDAP server. Leave this blank to have phpLDAPadmin
   auto-detect it for you. */
$servers->setValue('server','base',array('dc=olei,dc=me'));
```
> `host`填自己`server`端的IP  
`dc`填写自己的`dc`名，上面是我的`dc`名
3. 还有这么一行：

```
$servers->setValue('login','bind_id','cn=admin,dc=olei,dc=me');
```
### 访问
- 安装php7.0，nginx

```
$ sudo apt-get install php7.0 php7.0-pfm nginx
```
- 配置nginx信息
 > `/etc/nginx/conf.d`里面添加一个`ldap.conf` 文件：
  - http:

```
server {
        server_name 127.0.0.1;
        listen 80;

# document root
        root /usr/share/nginx/www;
        index index.php index.html index.htm;

# application: phpldapadmin
       location /phpldapadmin {
       alias /usr/share/phpldapadmin/htdocs;
       index index.php index.html index.htm;

  }
       location ~ ^/phpldapadmin/.*\.php$ {
       root /usr/share;
       if ($request_filename !~* htdocs) {
            rewrite ^/phpldapadmin(/.*)?$ /phpldapadmin/htdocs$1;
     }
       fastcgi_pass unix:/run/php/php7.0-fpm.sock;
       fastcgi_index index.php;
       fastcgi_param SCRIPT_FILENAME $request_filename;
       include fastcgi_params;
 }

# logging
    error_log /var/log/nginx/phpldapadmin.error.log;
    access_log /var/log/nginx/phpldapadmin.access.log;
}
```
   - https:

```
server {
	listen 443 ssl http2;	
        server_name i1995.cn;
       # listen 80;

# document root
        root /usr/share/nginx/www;
        index index.php index.html index.htm;
	
	ssl on;
        ssl_certificate /etc/letsencrypt/live/i1995.cn/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/i1995.cn/privkey.pem;
        ssl_ciphers 'ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4';
        ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        ssl_session_cache  builtin:1000  shared:SSL:10m;
        ssl_session_timeout  5m;
# application: phpldapadmin
       location /phpldapadmin {
       alias /usr/share/phpldapadmin/htdocs;
       index index.php index.html index.htm;
  }
       location ~ ^/phpldapadmin/.*\.php$ {
       root /usr/share;
       if ($request_filename !~* htdocs) {
            rewrite ^/phpldapadmin(/.*)?$ /phpldapadmin/htdocs$1;
     }
       fastcgi_pass unix:/run/php/php7.0-fpm.sock;
       fastcgi_index index.php;
       fastcgi_param SCRIPT_FILENAME $request_filename;
       include fastcgi_params;
 }

# logging
    error_log /var/log/nginx/phpldapadmin.ssl.error.log;
    access_log /var/log/nginx/phpldapadmin.ssl.access.log;
}
```
> https的证书申请方式请查看：[ubuntu 16.04－nginx－ssl](http://blog.olei.me/2017/10/18/certbot%E5%85%8D%E8%B4%B9SSL%E8%AF%81%E4%B9%A6%E7%94%B3%E8%AF%B7%E9%83%A8%E7%BD%B2/#more)  
> 之后访问https://domain/phpldapadmin  
> 比如：我的https://i1995.cn/phpldapadmin  
![](https://images.lwg1995.cn/phpldapadmin.png)  
---
