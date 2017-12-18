---
title: docker基础命令浅谈
date: 2017-10-13 23:04:32
tags: docker
categories: "技术"
copyright: true
---
### 镜像
- 下载镜像

```
$ docker pull  镜像名
```
<!-- more -->

- 删除镜像

```
$ docker rmi xxx
```
> xxx为仓库源或者镜像ID  
> 若无法删除，则删除其依赖  
> 
```
docker rm `docker ps -aq`
```
- 导入导出镜像  
  
1.  导出镜像
```
$ docker save -o xxx.tar xxx
```
2.  导入本地镜像

```
$ docker load --input xxx.tar
```
3.  从一台机器迁移到另外一台机器

```
$ docker save xxx | bzip2 | ssh ....@.... "cat  |  docker load "
```

> xxx为镜像名  
### 容器
- 以shell运行容器

```
$ docker  run  -it  linux镜像名  /bin/bash
```
- 后台启动容器

```
$ docker  run  -d ...
```
- 不进入容器，对该容器进行命令行操作

```
$ docker exec -it 容器名 command1
```
### 第三方仓库操作
> 已腾讯云的为例  
- 在腾讯云上面的容器服务中，创建自己的镜像，然后创建镜像
  - 我的仓库名为`myrepo`
  - 我的地址为:`ccr.ccs.tencentyun.com/myrepo`
- 登录(以我的qq为例)

```
$ docker login --username=986728544 ccr.ccs.tencentyun.com
```
- 获取上面的镜像(加入我有redis镜像)

```
$ docker pull ccr.ccs.tencentyun.com/myrepo/redis:[tag]
```
- 将本地镜像推到上面去

```
$ docker tag [ImageId] ccr.ccs.tencentyun.com/myrepo/redis:[tag]
$ docker push ccr.ccs.tencentyun.com/myrepo/redis:[tag]
```
> [tag]填写腾讯云上面的镜像版本
