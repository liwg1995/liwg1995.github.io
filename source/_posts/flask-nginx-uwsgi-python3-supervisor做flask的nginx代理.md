---
title: flask + nginx + uwsgi + python3 + supervisor做flask的nginx代理
date: 2017-12-11 15:17:52
tags: flask
---
> ubuntu 16.04为例
### 安装`nginx`

```
apt-get install nginx
```
<!--more-->

### 安装`python`虚拟环境
- 安装`virtualenvwrapper`

```
pip install virtualenvwrapper
```
- 在根目录下的`.zshrc`或者`.bashrc`添加：

```
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```
- 之后运行：

```
source .zhsrc
```
或者

```
source .bashrc
```
### 配置虚拟环境
- 创建`python3`的虚拟环境：

```
mkdirvirtualenv /usr/bin/python3
```
- 安装`flask`

```
pip install flask
```
- 安装`uwsgi`

```
pip install uwsgi
```
### 安装`supervisor`

```
apt-get install supervisor
```
> 我们在`root`的根目录下创建项目

### `hello world`项目为例
- 创建`flask`文件夹，写入`hello.py`文件

```
mkdir flask
```

```
vim hello.py
```

```
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "Hello World!"
if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5009)
```
- 创建`hello.ini`文件，为了`uwsgi`配置

```
vim hello.ini
```

```
[uwsgi]
wsgi-file = hello.py
chdir = /root/flask
socket = 127.0.0.1:5009
callable = application
processes = 10
threads = 2
stats = 127.0.0.1:9191
```
> 解析输入`uwsgi --ini hello.ini`就可以启动`uwsgi`了，但是退出就又没有了，我们用`supervisor`
- 在`/etc/supervisor/conf.d`下创建`hello.conf`文件：

```
vim hello.conf
```

```
[program:flask]
# 启动命令入口
command=/root/.virtualenvs/py3_flask/bin/uwsgi /root/flask/hello.ini

# 命令程序所在目录
directory=/root/flask
#运行命令的用户名
user=root

autostart=true
autorestart=true
```
- `/etc/nginx/conf.d`下创建`hello.conf`用于`nginx`的代理

```
vim hello.conf
```

```
server {
    listen 5002;
    server_name IP/domain_name;
    charset utf-8;
    client_max_body_size 75M;
    location / {
    include uwsgi_params;
    uwsgi_pass 127.0.0.1:5009;
    uwsgi_param UWSGI_PYTHON /root/.virtualenvs/py3_flask;
    uwsgi_param UWSGI_CHDIR /root/flask;
    uwsgi_param UWSGI_SCRIPT hello:app;
    }
}
```
### 重启服务
- 重启`nginx`

```
/etc/init.d/nginx restart
```
- 重启`supervisor`

```
/etc/init.d/supervisor restart
```
### 访问:

```
http://ip:5002
```

