---
title: hexo next中接入DaoVoice
date: 2017-10-25 14:15:06
tags: 工具
categories: 建站
---
### 注册
- 官方网站：[DaoVoice](http://www.daovoice.io/)
- 填写信息：

<!--more-->

![](http://onsjbscqd.bkt.clouddn.com/image/daovoice_05.png)
- 进入`应用设置` -> `安装到网站`
![](http://onsjbscqd.bkt.clouddn.com/image/daovoice_03.png)
- 获取`接入代码`和`app_id`
![](http://onsjbscqd.bkt.clouddn.com/image/daovoice_08.png)

### 编写主题配置文件
- 找到`themes/next/_config.yml`目录
- 增加以下配置，其中 `app_id` 就是上图中红框中的 `app_id`

```
daovoice:
  enable: true
  app_id: ******
```
### 编写`DaoVoice`接入代码
- 找到`themes/next/layout/_third-party/`目录
- 在该目录下新增`daovoice/daovoice.swig` 文件
- `daovoice.swig`文件中写入`DaoVoice` 接入代码

```
{% if theme.daovoice.enable %}
   <script>
     (function(i,s,o,g,r,a,m){
     i["DaoVoiceObject"]=r;
     i[r]=i[r]||function(){
     (i[r].q=i[r].q||[]).push(arguments)},
     i[r].l=1*new Date();
     a=s.createElement(o),
     m=s.getElementsByTagName(o)[0];
     a.async=1;
     a.src=g;
     a.charset="utf-8";
     m.parentNode.insertBefore(a,m)
     })(window,document,"script",('https:' == document.location.protocol ? 'https:' : 'http:') + "//widget.daovoice.io/widget/{{ theme.daovoice.app_id }}.js","daovoice")
     daovoice('init', {
     app_id: "{{ theme.daovoice.app_id }}"
     });
     daovoice('update');
   </script>
 {% endif %}
```
### 在模板文件里引入`DaoVoice`接入代码
- 找到`themes/next/layout/_layout.swig`文件
- 引入刚才我们上面写的 daovoice.swig 文件

```
{% include '_third-party/daovoice/daovoice.swig' %}
```
### 检验是否接入成功
- 运行如下：


```
$ hexo g    // 生成静态文件
$ hexo s    // 在本地起测试服务器
```
- 查看 `http://localhost:4000/` 页面是否右下角已经有了 `DaoVoice` 小图标
![](http://onsjbscqd.bkt.clouddn.com/image/daovoice_07.png)
- 之后运行：

```
$ hexo d   #上传到github上
```
> `DaoVoice` 的这个小图标可以自定义样式和位置, 可以调整到你喜欢的位置  
引用：http://olivewind.com/2017/17040305/
