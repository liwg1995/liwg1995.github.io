---
title: gitlab的安装，更新，备份恢复
date: 2017-10-20 17:39:11
tags: gitlab
categories: 技术
copyright: true
---
### 安装
- 配置国内镜像源
> 由于国内镜像，博主我只找到清华大学的镜像中有`gitlab-ce`，所以我们就配置好清华大学的镜像 
1.  添加GitLab 的 GPG 公钥:

```
$ curl https://packages.gitlab.com/gpg.key 2> /dev/null | sudo apt-key add - &>/dev/null
```
<!--more-->

2. 在`/etc/apt/sources.list.d/gitlab-ce.list`写入：  

```
deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu xenial main
```
- 国外镜像添加的是:  
```
deb https://packages.gitlab.com/gitlab/gitlab-ce/ubuntu/ xenial main
```
> 国外的镜像在国内的话，连接不是很稳定，有时候网速贼快，有时候又特么忒慢，建议使用国内源`(也不是很稳定)`
3. 安装

```
$ sudo apt-get update
$ sudo apt-get install gitlab-ce
```
4. 配置
-  修改`/etc/gitlab/`目录中的`gitlab.rb`文件，配置域名，配置邮箱等等
-  重新配置命令:

```
$ sudo gitlab-ctl reconfigure
```
5. 运行

```
$ sudo gitlab-ctl restart
```
### 备份恢复
1. 备份

```
$ sudo gitlab-rake gitlab:backup:create
```
> 在`/var/opt/gitlab/backups`目录下会有类似如下格式的备份文件:  
`1466811825_gitlab_backup.tar`
2. 恢复
- 恢复
```
# 停止相关数据连接服务
$ sudo gitlab-ctl stop unicorn 
$ sudo gitlab-ctl stop sidekiq

$ sudo gitlab-rake gitlab:backup:restore BACKUP=xxxx   # xxx为备份文件_gitlab_backup.tar前面的数字
```
- 启动

```
$ sudo gitlab-ctl start
```
### 更新

```
$ sudo apt-get update
$ sudo apt-get install gitlab-ce
$ sudo apt-get reconfigure
```
---
