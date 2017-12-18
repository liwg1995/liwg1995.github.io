---
title: ubuntu下使用ldap做服务器用户认证
date: 2017-11-03 19:40:21
tags: openldap
categories: 技术
---
### `server`端
- 安装`openldap`
  - 参考:[openldap安装](http://blog.olei.me/2017/10/18/ubuntu16-04%E5%AE%89%E8%A3%85openldap%E4%BB%A5%E5%8F%8Aphpldapadmin/)
- 安装`migrationtools`
<!--more-->

```
$ apt-get install migrationtools
```
  - 修改`migrate_common.sh`，该文件在`/etc/migrationtools`文件夹下

```
$ DEFAULT_MAIL_DOMAIN = "olei.me"; 
$ DEFAULT_BASE = "dc=olei,dc=me";
```
> 以上的东西改成自己的

- 配置

```
$ cd /usr/share/migrationtools
$ ./migrate_base.pl > /tmp/base.ldif
$ ./migrate_passwd.pl /etc/passwd> /tmp/passwd.ldif
$ ./migrate_group.pl /etc/group> /tmp/group.ldif
$ ldapadd -x -D "cn=admin,dc=olei,dc=me" -W -f /tmp/base.ldif
$ ldapadd -x -D "cn=admin,dc=olei,dc=me" -W -f /tmp/passwd.ldif
$ ldapadd -x -D "cn=admin,dc=olei,dc=me" -W -f /tmp/group.ldif
```
### `client`端
#### 手动方式
## 手动方式
### 安装软件

```
$ apt-get install ldap-utils libpam-ldap libnss-ldap nslcd
```
> 期间配置一些server端的信息  
### 认证方式中添加ldap

```
$ auth-client-config -t nss -p lac_ldap
```
### 查看/etc/nsswitch.conf
```
$ vim /etc/nsswitch.conf
```

```
# 可以看到多了如下的东西
passwd:     files ldap
group:      files ldap
shadow:     files ldap
```



### 使认证通过后自动创建用户家目录

```
$ vim /etc/pam.d/common-session
```
> 添加：session required pam_mkhomedir.so skel=/etc/skel umask=0022 

### 执行

```
$ /etc/pam.d/libnss-ldap restart
```
### 配置可在本机通过passwd更改用户密码

```
$ vim /etc/pam.d/common-password  #除去其中的use_authtok参数
$ /etc/init.d/nscd restart  
```
- 登陆或切换用户时即通过ldap进行认证，如切换为ldap中的用户manager  

```
$ su - manager
```
> Password:*****   
Creating directory '/home/manager'. 
manager@ldapclient:~$  

#### 脚本方式
- 代码：
```
#!/bin/bash  
  
#--------------------------------------------------------------------------------  
  
#Ldap server地址及base DN  
LDAP_SERVER_IP=xxx.xx.xx.xx  
BASE_DN='dc=olei,dc=me'  
  
#--------------------------------------------------------------------------------  
  
#创建preseed文件-软件安装自应答  
touch debconf-ldap-preseed.txt  
echo "ldap-auth-config    ldap-auth-config/ldapns/ldap-server    string    ldap://$LDAP_SERVER_IP" >> debconf-ldap-preseed.txt  
echo "ldap-auth-config    ldap-auth-config/ldapns/base-dn    string    $BASE_DN" >> debconf-ldap-preseed.txt  
echo "ldap-auth-config    ldap-auth-config/ldapns/ldap_version    select    3" >> debconf-ldap-preseed.txt  
echo "ldap-auth-config    ldap-auth-config/dbrootlogin    boolean    false" >> debconf-ldap-preseed.txt  
echo "ldap-auth-config    ldap-auth-config/dblogin    boolean    false" >> debconf-ldap-preseed.txt  
echo "nslcd   nslcd/ldap-uris string  ldap://$LDAP_SERVER_IP" >> debconf-ldap-preseed.txt  
echo "nslcd   nslcd/ldap-base string  $BASE_DN" >> debconf-ldap-preseed.txt  
  
cat debconf-ldap-preseed.txt | debconf-set-selections  
  
#安装ldap client相关软件  
apt-get install -y ldap-utils libpam-ldap libnss-ldap nslcd  
  
#认证方式中添加ldap  
auth-client-config -t nss -p lac_ldap  
  
#认证登录后自动创建用户家目录  
echo "session required pam_mkhomedir.so skel=/etc/skel umask=0022" >> /etc/pam.d/common-session  
  
#自启动服务 
/etc/init.d/libnss-ldap restart
update-rc.d nslcd enable  
  
#可以在Host上通过passwd更改用户密码  
cp /etc/pam.d/common-password /etc/pam.d/common-password.bak  
sed -i 's/use_authtok//' /etc/pam.d/common-password  
  
#使配置生效  
/etc/init.d/nscd restart  
```
---
