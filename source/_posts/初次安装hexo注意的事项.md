---
title: 初次安装hexo注意的事项
date: 2017-09-30 21:39:52
tags: "建站"
categories: "建站"
copyright: true
---

### 前期需要安装的组件
- nodejs  
 - 下载[nodejs](https://nodejs.org)
   - windows:[windows](https://nodejs.org/dist/v6.11.3/node-v6.11.3-x64.msi)
   - linux（`ubuntu`为例）

<!-- more -->  
 
```
$ sudo add-apt-repository ppa:chris-lea/node.js
$ sudo apt-get update
$ sudo apt-get install nodejs
```
  
- 或者采用`nvm`安装：  
`$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh`
`$ nvm install 4`  

> 主要是要用到npm
  > 国内的话，npm install会超级慢，天朝墙很高，你有能耐就翻过去咯~
- git
> 既然是要跟github打交道，那么git组件是必不可少的了

### 翻墙问题
#### 我要是没有那个能耐怎么办呢？  
- 国内有很好的镜像源，比如清华源:[清华源](https://mirrors.tuna.tsinghua.edu.cn/)等等  
#### 我们用淘宝源，来安装一个国内比较快的命令`cnpm`来代替`npm`  
`$ npm install -g cnpm --registry=http://registry.npm.taobao.org`  
> 因为我们安装好npm之后，之后要安装很多npm的组件，都要用到`npm install`，会超级慢，所以我们用`cnpm`来代替`npm`

### 选择镜像
#### 我们安装好`hexo`的时候，要运行`hexo`的一些命令，会发现特喵还是这么慢，怎么破？
- 我们安装`nrm`来选择镜像  
`$ cnpm install -g nrm --save`  
- 选择镜像  
`$ nrm use taobao`  
> 这样我们就选择好了国内镜像  

### 咋个安装`hexo`啊？
- `cnpm`安装好了，我们就用  
`$ cnpm install -g hexo-cli`
`$ cnpm install -g hexo`
### `github`上注意的东西
1. `github`的`repo`的名称必须是`用户名.github.io`
 - 比如：我的`github`用户名为`keykeyorange`,那么我的用户名就为：  
  - `keykeyorange.github.io`
2. 记得在`github`上面添加本地电脑的`.ssh`目录的`id_rsa.pub`
> 具体怎么生成，请百度：[baidu](https://www.baidu.com)

### 注意两个命令
1. `hexo g`
 - 第一次或许会提醒缺少组件，自己通过`cnpm`安装就对了
 - 这个命令相当于`git`命令中的`git commit -a -m xxxxx`
2. `hexo d`
 - 这个命令相当于`git`命令中的`git push`
