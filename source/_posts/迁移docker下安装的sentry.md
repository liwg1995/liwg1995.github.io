---
title: 迁移docker下安装的sentry
date: 2017-10-16 18:16:30
tags: "docker"
categories: 技术
copyright: true
---
### 还原postgresql数据库
1.停止web,base,worker,cron容器

```
$ docker stop ID  #ID为容器的id
```
2.备份`postgres`数据库

```
$ docker exec -t onpremise_postgres_1 pg_dumpall -U postgres > ~/data/all.sql
```
<!--more-->

> 将备份的文件传到需要还原的机器上  
> 还原的服务器也需要停止上面的那些容器

3.进入`postgresql`容器删除`postgres`数据库，并再次创建

```
$ docker exec -it onpremise_postgres_1 psql -d template1 -U postgres  
template1=#  drop database postgres
template1=#  create database postgres
```
3.还原之前备份的数据库

```
$ cat all.sql | docker exec -i onpremise_postgres_1 psql -U postgres
```
4.启动停止的`容器`

```
$ docker start ID  #ID为容器的id
```
> 访问：http://ip:9000
