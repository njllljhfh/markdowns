## 一、李尓项目中用到的

### 1、Linux

```python
# 升级pip
pip install --upgrade pip

# 查看用户开启的进程的    端口号
netstat -tuplt

# 查看动态实时日志
cd /home/njl/Desktop/mvp-python/logs
tail -f mvp.log

```



### 2、nginx    ubuntu

#### 2.1、常用命令

```python
# ubuntu安装
sudo apt-get install nginx

安装好的文件位置：

/usr/sbin/nginx：主程序

/etc/nginx：存放配置文件

/usr/share/nginx：存放静态文件

/var/log/nginx：存放日志


# 启动 nginx
sudo /usr/sbin/nginx

# 从容停止 nginx
sudo kill -QUIT master的进程号

# 重启 nginx
sudo nginx -s reload

# 查看 nginx 是否启动
ps -aux | grep nginx
```



#### 2.2 、配置

```python
一、在  /etc/nginx/nginx.conf 中（nginx的总配置）
http {
		client_max_body_size 100m;  # 设置最大上传
}

二、自定义的配置 /etc/nginx/conf.d/mvp.conf
  1 # uwsgi 的配置
  2 upstream mvp {
  3     server 127.0.0.1:8989;
  4 }
  5 
  6 
  7 # Nginx 的配置
  8 server {
  9     listen  8000;
 10     server_name 0.0.0.0;
 11 
 12     location /static {
 13         alias   /home/njl/Desktop/mvp-python/static;
 14     }
 15 
 16     location = / {
 17         root    /home/njl/Desktop/mvp-python/static;
 18         index   index.html  index.htm;
 19     }
 20 
 21     location = /index.html {
 22         root    /home/njl/Desktop/mvp-python/static;
 23         index   index.html  index.htm;
 24     }
 25 
 26     # 动态文件访问地址
 27     location / {
 28        include uwsgi_params;
 29        uwsgi_pass mvp;
 30 
 31       add_header Access-Control-Allow-Origin *;
 32       add_header Access-Control-Allow-Methods "POST, GET, DELETE, PUT, OPTIONS, PATCH, HEAD, TRACE";
 33       add_header Access-Control-Allow-Headers "Origin, Authorization, Accept";
 34       add_header Access-Control-Allow-Credentials true;
 35       add_header Access-Control-Allow-Headers Origin,X-Requested-With,Content-Type,Accept;
 36 
 37 
 38     }
 39 
 40     # err_page  500 502 503 504 /50x.html;
 41     # location = /50x.html {
 42     #     root html;
 43     # }
 44 }

```



### 3、uwsgi

安装：pip install uwsgi        （***注意要在项目的环境下安装***）



#### 3.1、安装uwsgi报错

错误内容：plugins/python/uwsgi_python.h:2:20: fatal error: Python.h: 没有那个文件或目录

错误原因：python3.6中缺少

解决方案：sudo apt-get install libpython3.6-dev

[参考博客]: https://blog.csdn.net/jaket5219999/article/details/72954394
[参考博客]: http://blog.51cto.com/eminzhang/1285705



#### 3.2、常用命令

```python
# 开启uwsgi
cd /home/njl/Desktop/mvp-python/
uwsgi --ini uwsgi1.ini

# 停止uwsgi
uwsgi --stop /home/njl/Desktop/mvp-python/uwsgi1.pid

# 查看uwsgi进程
ps aux | grep uwsgi

```



#### 3.3、配置

```python
[uwsgi]
#使用nginx连接时使用，Django程序所在服务器地址
socket = 127.0.0.1:8989
#直接做web服务器使用，Django程序所在服务器地址
#http=127.0.0.1:8989
#项目根目录
#chdir = /home/dev/mvp/MVI_KIT
chdir = /home/njl/Desktop/mvp-python
#项目中wsgi.py文件的目录，相对于项目目录
wsgi-file = MVI_KIT/wsgi.py
# 进程数
processes = 8
# 线程数
threads = 8
# uwsgi服务器的角色
master = True
# 存放进程编号的文件
# pidfile = /var/run/uwsgi.pid
pidfile = /home/njl/Desktop/mvp-python/uwsgi1.pid
# 日志文件，因为uwsgi可以脱离终端在后台运行，日志看不见。我们以前的	#python  manager  runserver是依赖终端的
# daemonize = /home/dev/mvp/logs/uwsgi.log
daemonize = /home/njl/Desktop/mvp-python/uwsgi1.log
# 指定依赖的虚拟环境 所有用的包都在虚拟环境里
#virtualenv = /home/dev/mvp/venv
virtualenv = /home/njl/.virtualenvs/venv_mvp


# 最大连接数
max-requests = 10000
# 客户端上传数据的大小限制
buffer-size = 102400k
```



### 4、egg包 

```python
# 安装egg包
easy_install ~/Desktop/ImageAlgrithm-1.0-py3.6.egg 

# 卸载egg包
# 注意：执行完下面的卸载命令后，要在项目的环境中，手动的将该包的文件夹删除。
easy_install -m ImageAlgrithm



```



## 二、linux学习



### 1、

```python
1. /etc/profile: 此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行. 
另外, /etc/profile 中设定的变量(全局)的可以作用于任何用户,而 ~/.bashrc 等中设定的变量(局部)只能继承 /etc/profile 中的变量,他们是"父子"关系. 


```



