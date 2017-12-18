---
title: php+nginx+http/https的Basic Authentication认证
date: 2017-10-19 19:31:20
tags:
 - nginx
 - Basic Authentication
categories: "技术"
copyright: true
---
### 写入帐号密码

```
$ sudo printf "xxxxx:$(openssl passwd -crypt *****)\n" >> /etc/nginx/pass_file
```
> `xxxxx`为用户名，`*****`为该帐号的密码
### `nginx`配置文件

<!--more-->

```
location / {
    	auth_basic "Restricted";
        auth_basic_user_file /etc/nginx/pass_file;
        root /var/www/satis/web;
        try_files $uri $uri/ /index.php?$query_string;
        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            include        fastcgi_params;
            fastcgi_param  SCRIPT_FILENAME  $realpath_root/$fastcgi_script_name;
        }
    }
```

> auth_basic_user_file /etc/nginx/pass_file;`
- 注意这里的路径，错了会一直`404`
