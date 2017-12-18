---
title: docker下安装sentry
date: 2017-10-09 18:25:38
tags: docker,sentry
categories: "技术"
copyright: true
---
### `安装Docker`
- 脚本

```
$ wget -qO- https://get.docker.com/ | sh
```
- 源

```
$ curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

$ sudo add-apt-repository \
    "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
$ sudo apt-get update
$ sudo apt-get install docker-ce
```
<!-- more -->

### 安装`docker-compose`

```
$ pip install -U docker-compose
```
> 如果提示没有pip，则需要安装pip
```
$ apt-get install python-pip
```
### 获取`sentry`

```
git clone https://github.com/getsentry/onpremise.git
```
> cd到onpremise目录下  

1.构建容器并创建数据库和sentry安装目录

```
$ mkdir  -p data/{sentry,postgres}
```
2.配置国内源

```
$ cd /etc/docker
$ touch daemon.json
```
> 在`daemon.json`里面写入：

```
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn/"]
}
```

```
$ service docker restart
```
3.生成secret key并添加到docker-compose文件里

```
$ docker-compose run --rm web config generate-secret-key
```
4.重建数据库，并创建sentry超级管理员用户

```
$ docker-compose run --rm web upgrade
```
5.启动所有的服务

```
$ docker-compose up -d
```
### 访问
- 访问`http://ip:9000`
  - ip为你本地或者服务器的ip
> 以上是ubuntu 16.04下进行
