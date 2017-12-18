---
title: Ubuntu搭建DNS服务器
date: 2017-10-27 14:07:00
tags: DNS服务器
categories: 技术
copyright: true
---
>系统环境:  
Ubuntu 16.04.3 LTS  
内网IP : 192.168.1.110

### 安装

```
$ apt-get update
$ apt-get install bind9
```
<!--more-->

### 配置缓存转发
- 打开打开`/etc/bind/named.conf.options`,修改后如下:

```
acl goodclients {
        192.168.1.0/24;
        localhost;
        localnets;
};

options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0's placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };

        //added
        listen-on { 192.168.1.110;};

        recursion yes ;
        allow-query { goodclients;};
        allow-transfer { none; }; # disable zone transfers by default

        forwarders {
                223.5.5.5; # alidns
                223.6.6.6; # alidns
        };
        forward only ;

};
```
### 配置local文件
- `named.conf.local` 文件默认是空的。本文在配置文件中分别定义一条`正向解析`和一条`反向解析`。配置文件修改后类似如下:

```
//domain->ip
zone "local.com" in {
        type master;
        file "/var/cache/bind/db.local.com";
};

//ip->domain
zone "1.168.192.in-addr.arpa" in {
        type master;
        file "/var/cache/bind/db.1.168.192";
};
```
### 定义区域配置文件
> 配置文件定义后类似如下，分别是正向和反向两个解析记录。按照自己需求修改相应的区域和区域解析记录，IP等信息。
- 正向记录

```
$ sudo vim /var/cache/bind/db.local.com
```

```
$TTL 604800
@ IN SOA local.com. root.local.com. (
2 ; Serial
604800 ; Refresh
86400 ; Retry
2419200 ; Expire
604000) ; Negative Cache TTL
;
; name servers
@ IN NS ns.local.com.
@ IN A 192.168.1.110
;ns records
ns IN A 192.168.1.110
;host records
www IN A 192.168.1.110
api IN A 192.168.1.100
```
- 反向记录

```
$ sudo vim /var/cache/bind/db.1.168.192
```

```
$TTL 604800
@ IN SOA local.com. root.local.com. (
2 ; Serial Number
604800 ; Refresh
86400 ; Retry
2419200 ; Expire
86400 ); ; Minimum

@ IN NS local.com.

66 IN PTR www.local.com.
66 IN PTR api.local.com.
```
> 以上两个文件均写在`/var/cache/bind` 下
### 检查配置、重启
- 检查

```
$ cd /var/cache/bind
$ named-checkzone local.com /var/cache/bind/db.local.com
```
输出：  

```
zone local.com/IN: loaded serial 2
OK
```

```
$ named-checkzone db.1.168.192 /var/cache/bind/db.1.168.192
```
输出：

```
zone db.1.168.192/IN: loaded serial 2
OK
```
> 分别检查了语法和区域配置文件，没有报错。重启bind服务。

- 重启

```
$ sudo service bind9 restart
```
> 到这里DNS服务器的配置就完成了
### 配置路由器首要DNS
- 在路由器里设置`首要DNS` 为 `192.168.1.110` ，这样我们就可以在同一个内网下访问：`www.local.com` 就会指向到 `192.168.1.110`，访问：`api.local.com` 就会指向到 `192.168.1.100`
