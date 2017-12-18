---
title: composer利用satis创建私人仓库
date: 2017-10-24 17:17:33
tags:
 - composer
 - satis
categories: "技术"
copyright: true
---
### composer安装

```
$ curl -sS https://getcomposer.org/installer | php
$ mv composer.phar /usr/local/bin/composer
```
### 生成satis

```
$ composer create-project composer/satis --stability=dev --keep-vcs
```
<!--more-->

- 创建`statis.json`，配置如下：

```
{
 	 "name": "My Repository",
 	 "homepage": "http://packages.example.org",
 	 "repositories": [
   		 { "type": "vcs", "url": "https://github.com/mycompany/privaterepo" },
   		 { "type": "vcs", "url": "http://svn.example.org/private/repo" },
   		 { "type": "vcs", "url": "https://github.com/mycompany/privaterepo2" }
 	 ],
 	 "require-all": true
}
```
- require-all:true表示全部的包，需要指定包的，需要更改为：

```
"require": {
 	  "company/package": "*",
  	  "company/package2": "*",
  	  "company/package3": "2.0.0"
｝
```
### 安装web端

```
$ php bin/satis build satis.json public/
```
> satis.json就是配置的文件，public时生成的管理网站  

### 部分更新
- 单独的一个存储库的话

```
$ php bin/satis build --repository-url https://only.my/repo.git satis.json web/
```
### 使用私有源
- 只需要在项目的 composer.json 文件的根上添加

```
{
  "repositories": [
    {
      "type": "composer",
      "url": "http://satis仓库地址/"
    }
  ],
  "require": {
    "company/package": "1.2.0",
    "company/package2": "1.5.2",
    "company/package3": "dev-master"
  }
}
```
- 然后执行`composer update`即可
  - 注意：源里面只有“仓库列表”，并没有真的同步代码仓库过来，所以下载还要走托管代码的机器，比如 GitHub，内部 GitLab 等。
- 如果从 clone 速度太慢了，我们也可以缓存在我们的仓库中,在satis.json中增加:

```
{
    "archive": {
        "directory": "dist",
        "format": "tar",
        "prefix-url": "http://packages.dev.com/",
        "skip-dev": true
    }
}
```
*1. directory: 必需要的，表示生成的压缩包存放的目录，会在我们build时的目录中
2. format: 压缩包格式, zip（默认） tar
3. prefix-url: 下载链接的前缀的Url,默认会从homepage中取
4. skip-dev: 默认为假，是否跳过开发分支
5. absolute-directory: 绝对目录
6. whitelist: 白名单，只下载哪些
7. blacklist: 黑名单，不下载哪些
8. checksum: 可选，是否验证sha1*
- 然后重新再次生成就ok了：

```
$ php bin/satis build satis.json public/
```
> 会发现public目录多了一个dist目录，里面有很多tar的压缩包，这就是我们的package。 之后再执行composer update就会发现快了很多。

