# 第一章、用 docker 制作镜像



## docker常用命令

```shell
# 查看本地镜像
docker images

# 查看所有运行或者不运行容器
docker ps -a

#停止所有容器运行
docker stop $(docker ps -a -q)

#删除所有停止运行的容器
docker rm $(docker ps -a -q)

# 基于容器打包成镜像
语法：docker commit -m '改动信息' -a "作者信息" [container_id] [new_image:tag]

# 从容器拷贝文件到宿主机
docker cp 容器名:容器中要拷贝的文件名及其路径 要拷贝到宿主机里面对应的路径

# 从宿主机拷贝文件到容器
docker cp 宿主机中要拷贝的文件名及其路径 容器名:要拷贝到容器里面对应的路径


```



## 一、安装docker

[官网](https://docs.docker.com/engine/install/ubuntu/#installation-methods)





### docker命令每次需要sudo操作解决方案

[网址](https://blog.csdn.net/qq_26400953/article/details/79908941)

```
操作步骤如下
1.创建docker组：sudo groupadd docker
2.将当前用户加入docker组：sudo gpasswd -a ${USER} docker
3.重启服务：sudo service docker restart
4.刷新docker成员：newgrp - docker
```





## 二、镜像-mysql5.7

### 2.1、pull mysql5.7 镜像，启动容器

```shell
# 拉取 mysql5.7 镜像
docker pull mysql:5.7

# 创建文件夹
sudo mkdir -p /mydocker/mysql/conf /mydocker/mysql/logs /mydocker/mysql/data 

# 创建容器
docker run -d -p 4306:3306 --name mysqldb -e MYSQL_ROOT_PASSWORD=mysql mysql:5.7-debian
#配置好后，打包成mysql:mysqldb5.7镜像，然后用mysql:mysqldb5.7镜像启动容器
docker run -d -p 4306:3306 --name mysqldb mysql:mysqldb5.7
# 拷贝容器内的mysql配置目录到宿主机挂载目录
sudo docker cp mysqldb:/etc/mysql/mysql.conf.d /mydocker/mysql/conf

# 用新镜像启动mysql,将数据,日志,配置文件映射到本机
#docker run -d -p 4306:3306 --name mysqldb -v /mydocker/mysql/conf/mysql.conf.d:/etc/mysql/mysql.conf.d -v /mydocker/mysql/logs:/var/log/mysql -v /mydocker/mysql/data:/var/lib/mysql mysql:mysqldb5.7
# 不挂载mysql配置
docker run -d -p 4306:3306 --name mysqldb -v /mydocker/mysql/logs:/var/log/mysql -v /mydocker/mysql/data:/var/lib/mysql mysql:mysqldb5.7

# 进入 mysql 容器
docker exec -it mysqldb /bin/bash

# mysql查询配置文件路径
mysqld --verbose --help |grep -A 1 'Default options'
# 输出结果：
Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf ~/.my.cnf

```

在局域网内，远程访问mysql的端口是 4306。



### 2.2、上传 dockerhub

```shell
#1.在 dockerhub 上创建名为 mysql 的立私有仓库

#2.在宿主机命令行登录 docker 账号
docker login

#3.将本地镜像 mysql:mysqldb5.7 打tag 
docker tag mysql:mysqldb5.7 njllljhfh/mysql:mysqldb5.7

#4.push 打过tag的镜像到 dockerhub
docker push njllljhfh/mysql:mysqldb5.7
```



## 三、镜像-ubuntu1804_django_uwsgi

```shell
# 拉取 ubuntu18.04-conda 镜像
# docker pull gouthamshiv/ubuntu-conda
docker pull ubuntu:18.04

# 项目代码在宿主机的目录
/home/mc5k/projects/adc5000/v50006000/MS_ADC

# 创建容器，挂载后端代码目录
docker run -itd --name django_uwsgi -p 7056:8000 -v /home/mc5k/projects/adc5000/v50006000/MS_ADC:/projects/MS_ADC ubuntu:18.04
# 镜像制作好后commit为ubuntu:18.04-py3.8
语法：docker commit -m '改动信息' -a "作者信息" [container_id] [new_image:tag]
# 用新的镜像启动django服务的容器
docker run -itd --name django_uwsgi -p 7056:8000 -v /home/mc5k/projects/adc5000/v50006000/MS_ADC:/projects/MS_ADC -v /mydocker/uwsgi/logs:/var/log/uwsgi ubuntu:django-py3.8-uwsgi

# 进入 django_uwsgi 容器
docker exec -it django_uwsgi /bin/bash
```



### 3.1、安装python3.8

[网址](https://blog.csdn.net/A33280000f/article/details/125848983)

```shell
sudo vim /etc/apt/sources.list
#进入sources.list后，按i输入，在最后添加以下的国内源：
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse


#输入命令更新系统软件源地址:
sudo apt-get update


#在 Ubuntu 18.中，python3 的默认版本为 3.6
$ python3 -V
Python 3.6.9


#安装依赖包
sudo apt update
sudo apt install software-properties-common


#添加 deadsnakes PPA 源
sudo add-apt-repository ppa:deadsnakes/ppa


#安装 python 3.8
sudo apt install python3.8


# 备份原先的软连接
sudo -s mv /usr/bin/python3 /usr/bin/python3.bak


# 新建python3.8的软连接
ln -s /usr/bin/python3.8 /usr/bin/python


# 下载最新的官方 pip
sudo apt install curl
cd ~
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py


# 安装 python-distutils
sudo apt-get install python3.8-distutilsl


# 直接获取 pip3.8
sudo python get-pip.py
```



### 3.2、安装项目需要的python包

```shell
# 切换到项目目录
cd /projects/MS_ADC

# 批量安装python包
pip install -r requirements.txt -i https://pypi.douban.com/simple/
```



### 3.3、添加环境变量

```shell
# 进入文件
vim ~/.bashrc

#在最下面添加adc5000需要的环境变量，保存并退出
export ADC_ENV=production

#更新添加的变量
source ~/.bashrc
```



### 3.4、启动django项目，测试环境，解决问题

```shell
# 启动django，解决报错
python manage.py runserver 0.0.0.0:8000
```

#### 解决cv2报错

```shell
# import cv2 报错：
ImportError: libGL.so.1: cannot open shared object file: No such file or directory

# 解决
apt install libgl1-mesa-glx
```



### 3.5、安装uwsgi

#### 安装uwsgi报错

```shell
Exception: you need a C compiler to build uWSGI
# 解决
sudo apt-get install gcc

 
找不到头文件 #include <Python.h>
# 解决
sudo apt-get install libpython3.8-dev

# 安装uwsgi
pip install uwsgi
```



#### 用uwsgi启动django

```shell
uwsgi --ini uwsgi.ini
```



#### uwsgi启动后，用浏览器发起请求，报编码错误

```shell
# 报错
UnicodeEncodeError: 'ascii' codec can't encode characters in position 85-86: ordinal not in range(128)

# 查看当前本机编码
locale
#可以发现值都是POSIX

# 安装locales
apt-get install locales

# 安装en_US.UTF-8
locale-gen en_US.UTF-8

# 打开文件 /etc/profile
vim ~/.bashrc
# 将下面内容添加到最后
export LC_ALL=en_US.UTF-8
# 刷新
source ~/.bashrc

```



#### 安装lsof

```
# 安装lsof
sudo apt-get install lsof
原文链接：https://www.linuxcool.com/azlmlzwsyjqy
```



#### 编写uwsgi.ini文件

宿主机路径：/home/mc5k/projects/adc5000/v50006000/MS_ADC/uwsgi.ini

```shell
[uwsgi]
uid = root
chdir = /projects/MS_ADC
wsgi-file = /projects/MS_ADC/config/wsgi.py
processes = 1
threads = 4
master = False
pidfile = /projects/MS_ADC/uwsgi.pid
chmod-socket = 777
#socket = /projects/MS_ADC/uwsgi.socket
socket=0.0.0.0:8000
buffer-size = 102400k
callable = application
socket-timeout = 300
http-timeout = 300
logformat-strftime = true
log-date = %%Y-%%m-%%d %%H:%%M:%%S
logformat = [UWSGI][%(ftime)][%(method)][%(status)][%(addr)][%(uri)]
daemonize = /var/log/uwsgi/uwsgi.log

```



#### 编写启动uwsgi.ini的启动脚本

宿主机路径：/home/mc5k/projects/adc5000/v50006000/MS_ADC/start_uwsgi.sh

```bash
#!/bin/bash
export SERVER_PORT=8000
export ADC_ENV=production
export LC_ALL=en_US.UTF-8

cd /projects/MS_ADC

pids=$(lsof -i:$SERVER_PORT | awk '{print $2}' | grep -v "PID" |tr -s '\n' ' ')
echo "pids=$pids"
if [[ ! ${pids} ]]; then
 echo "uwsgi-server is not running, begin to start it."
else
 kill -9 ${pids}
fi
sleep 1s

uwsgi --ini uwsgi.ini
echo "started uwsgi-server successfully"

```



#### 在宿主机启动容器内uwsgi

```shell
# /projects/MS_ADC/start_uwsgi.sh 是容器内的路径
docker exec django_uwsgi /projects/MS_ADC/start_uwsgi.sh
```



### 3.6、编写 uwsgi, celery 自动启动脚本

```shell
# 租宿主机
cd /home/mc5k/projects/adc5000/v50006000/MS_ADC
vim auto_start_uwsgi_celery.sh

# 脚本内容如下
#!/bin/bash
export SERVER_PORT=8000
export ADC_ENV=production
export LC_ALL=en_US.UTF-8

# auto start celery
cd /projects/MS_ADC
celery_pids=$(ps -aux | grep celery | awk '{print $2}' | grep -v "PID" |tr -s '\n' ' ')
echo "celery_pids=$celery_pids"
celery_arr=($celery_pids)
celery_arr_len=${#celery_arr[@]}
echo "celery_arr_len=$celery_arr_len"
if [[ ${celery_arr_len} -gt 1 ]]; then
  echo "celery-worker-infer is running."
else
  echo "celery-worker-infer is not running, begin to start it."
  nohup celery -A config.Celery worker -l info -n local6000_0 -c 1 -P threads -Q infer_task_v5000_6000 > /var/log/celery/celery_infer.log 2>&1 &
fi
echo "----------------------------"

# auto stast uwsgi
cd /projects/MS_ADC
pids=$(lsof -i:$SERVER_PORT | awk '{print $2}' | grep -v "PID" | tr -s '\n' ' ')
echo "pids=$pids"
if [[ ! ${pids} ]]; then
  echo "uwsgi-server is not running, begin to start it."
  uwsgi --ini uwsgi.ini
  echo "start uwsgi-server successfully"
else
  echo "uwsgi-server is running"
fi
echo "----------------------------"
```



### 3.7、重启 celery 的脚本

start_celery.sh

```shell
# 租宿主机
cd /home/mc5k/projects/adc5000/v50006000/MS_ADC
vim start_celery.sh

# 脚本内容如下
#!/bin/bash
export ADC_ENV=production
export LC_ALL=en_US.UTF-8

cd /projects/MS_ADC

celery_pids=$(ps -aux |grep celery | grep local | awk '{print $2}' | grep -v "PID" |tr -s '\n' ' ')
echo "celery_pids=$celery_pids"

if [[ ${celery_pids} ]]; then
  echo "stop all celery-worker"
  kill -9 ${celery_pids}
fi

nohup celery -A config.Celery worker -l info -n local6000_0 -c 1 -P threads -Q infer_task_v5000_6000 > /var/log/celery/celery_infer.log 2>&1 &
echo "start celery-worker-infer successfully"
echo "----------------------------"
```



### 3.8、上传 dockerhub

```shell
#将本地镜像 nginx:my_nginx 打tag 
docker tag ubuntu:django-py3.8-uwsgi njllljhfh/adc_server:django_py3.8_uwsgi

#push 打过tag的镜像到 dockerhub
docker push njllljhfh/adc_server:django_py3.8_uwsgi
```



## 四、镜像-nginx

### 4.1、pull nginx 镜像，启动容器

```shell
# 拉取镜像
docker pull nginx

# 在宿主机创建文件夹
sudo mkdir -p /mydocker/nginx/nginx /mydocker/nginx/logs


# 启动nginx容器
docker run -d --name nginx_server -p 8080:80 -p 7156:8156 -v /mydocker/nginx/logs:/var/log/nginx nginx
# 将nginx容器中的 /etc/nginx/conf.d 目录拷贝到宿主机 /mydocker/nginx/nginx/
sudo docker cp nginx_server:/etc/nginx/conf.d /mydocker/nginx/nginx/


# 用新的镜像启动nginx，在容器内部 8156是前端代理服务的端口，7756是后端代理服务的端口。
docker run -d --name nginx_server -p 8080:80 -p 7156:8156 -p 7756:7756 -v /mydocker/nginx/logs:/var/log/nginx -v /mydocker/nginx/nginx/conf.d:/etc/nginx/conf.d -v /home/mc5k/projects/adc5000/v50006000/html:/projects/adc5000/v50006000/html nginx:my_nginx

# 进入 nginx_server 容器
docker exec -it nginx_server /bin/bash

# 安装vim
https://segmentfault.com/a/1190000039969652
```

### 4.2、nginx配置

```shell
# 在宿主机
cd /mydocker/nginx/nginx/conf.d

# 创建配置文件 adc_v50006000.conf
vim adc_v50006000.conf

# 添加如下内容，保存并退出
upstream adc_uwsgi {
    server 10.0.2.20:7056;
}

server {
        listen 7756;
        server_name adc5000_server;
        location /
            {
                uwsgi_send_timeout 600;
                uwsgi_connect_timeout 600;
                uwsgi_read_timeout 600;
                include uwsgi_params;
                uwsgi_pass adc_uwsgi;
            }
        client_max_body_size 1000m;
        client_body_timeout 3600;
}

upstream adc50006000 {
    server 127.0.0.1:7756;
}

server {
        listen 8156;
        listen [::]:8156;

        root /projects/adc5000/v50006000/html;

        index index.html index.htm index.nginx-debian.html;

        server_name v5000_6000;

        location / {
                try_files $uri $uri/ /index.html;
        }

        location /api {
            proxy_read_timeout 3600;
            proxy_pass http://adc50006000;
        }
        client_max_body_size 1000m;
}


# 进入nginx容器，重新加载nginx
nginx -s reload

# 此时web页面应该可以正常访问了
10.0.2.20:7156
```



### 4.3、上传 dockerhub

```shell
#将本地镜像 nginx:my_nginx 打tag 
docker tag nginx:my_nginx njllljhfh/nginx:my_nginx

#push 打过tag的镜像到 dockerhub
docker push njllljhfh/nginx:my_nginx
```



### 4.4、nginx 命令

```shell
# 修改配置后重载
sudo nginx -s reload
```





## 五、镜像-redis，minio，rabbitmq

```shell
# 启动容器
docker run -itd --name redis_mq_minio ubuntu:18.04-py3.8

# 进入 redis_mq_minio 容器
docker exec -it redis_mq_minio /bin/bash

```



### 5.1、安装 redis

```shell
apt-get update

sudo apt-get install redis

# 打开redis配置
vim /etc/redis/redis.conf
# 找到bind，注释小原有的bind，在下面添加 “bind 0.0.0.0” 保存退出
#bind 127.0.0.1 ::1
bind 0.0.0.0

# 启动redis
sudo /etc/init.d/redis-server restart

# 测试服务是否启动成功
redis-cli

# 在 ~/.bashrc 文件末尾添加启动项
内容如下：
redis_pids=$(ps -aux | grep redis | awk '{print $2}' | grep -v "PID" |tr -s '\n' ' ')
echo "redis_pids=$redis_pids"
redis_arr=($redis_pids)
redis_arr_len=${#redis_arr[@]}
echo "redis_arr_len=$redis_arr_len"
if [[ ${redis_arr_len} -gt 1 ]]; then
  echo "redis-server is running."
else
  echo "redis-server is not running, begin to start it."
  /etc/init.d/redis-server restart
fi
echo "----------------------------"


# 打包镜像
docker commit -m "redis" -a 'njl' b4795e88c1ee ubuntu:redis_mq_minio
```



### 5.2、安装 minio

```shell
/usr/local/bin# 启动容器
docker run -itd --name redis_mq_minio ubuntu:redis_mq_minio

# 进入 redis_mq_minio 容器
docker exec -it redis_mq_minio /bin/bash

mv /projects/MS_ADC/package/minio /usr/local/bin
cd /usr/local/bin
chmod +x minio

# 后台启动minio
nohup /usr/local/bin/minio server /data/minio --console-address :60006 > /var/log/minio/minio.log 2>&1 &


# 在 ~/.bashrc 文件末尾添加启动项，要安装 lsof 命令
内容如下：
# start minio
minio_pids=$(lsof -i:9000 | awk '{print $2}' | grep -v "PID" |tr -s '\n' ' ')
echo "minio_pids=$minio_pids"
minio_arr=($minio_pids)
minio_arr_len=${#minio_arr[@]}
echo "minio_arr_len=$minio_arr_len"
if [[ ${minio_arr_len} -ge 1 ]]; then
  echo "minio is running"
else
  echo "minio is not running, begin to start it."
  nohup /usr/local/bin/minio server /data/minio --console-address :60006 > /var/log/minio/minio.log 2>&1 &
fi
echo "----------------------------"


# 端口映射
docker run -itd --name redis_mq_minio -p 9379:6379 -p 9900:9000 -p 60006:60006 ubuntu:redis_mq_minio

# 访问宿主机ip  10.0.2.20:9900, 测试minio
```



### 5.3、安装 rabbitmq

```shell
sudo apt-get update

# 安装RabbitMQ
sudo apt-get install rabbitmq-server

# 重启
sudo service rabbitmq-server restart

↓↓↓================以下内容，在第一次执行 docker run 后，要进入容器中重新设置================↓↓↓
# 打开RABBITMQ管理工具
sudo rabbitmq-plugins enable rabbitmq_management

# 添加用户 
sudo rabbitmqctl add_user admin admin

# 设置用户权限 
sudo rabbitmqctl set_permissions -p / admin '.*' '.*' '.*'
sudo rabbitmqctl set_user_tags admin administrator

# 添加VHOST 
sudo rabbitmqctl add_vhost adcvhost

# 添加VHOST权限 
sudo rabbitmqctl set_permissions -p adcvhost admin ".*" ".*" ".*"
↑↑↑==================================================================================↑↑↑
```



### 5.4、启动 redis-mq-minio 容器

```shell
# 用最新的镜像启动 redis-mq-minio 容器
docker run -itd --name redis_mq_minio -p 9379:6379 -p 9900:9000 -p 60006:60006 -p 5672:5672 -p 15672:15672 -v /mydocker/minio/data:/data/minio -v /mydocker/minio/logs:/var/log/minio ubuntu:redis_mq_minio
```



### 5.5、上传 dockerhub

```shell
# 将本地镜像 ubuntu:redis_mq_minio 打tag 
docker tag ubuntu:redis_mq_minio njllljhfh/componet_server:redis_mq_minio

# push 打过tag的镜像到 dockerhub
docker push njllljhfh/componet_server:redis_mq_minio
```







-----------------------







# 第二章、基于制作的镜像在新服务器上部署



## 一、部署过程

### 1、从 dockerhub 拉取镜像

```shell
docker pull njllljhfh/mysql:mysqldb5.7
docker pull njllljhfh/nginx:my_nginx
docker pull njllljhfh/componet_server:redis_mq_minio
docker pull njllljhfh/adc_server:django_py3.8_uwsgi
```



### 2、启动 mysql 容器

```shell
# 启动容器
docker run -d -p 3306:3306 --name mysqldb -v /mydocker/mysql/logs:/var/log/mysql -v /mydocker/mysql/data:/var/lib/mysql njllljhfh/mysql:mysqldb5.7

# 初始化数据库
创建名称为 adc5000_v50006000 的数据库
字符集选择 utf8
排序规则 utf8_bin

# 运行初始化表的sql
/home/mc5k/projects/adc5000/v50006000/MS_ADC/builds/V5000_6000/V50006000_data_and_structure.sql

# 运行初始化缺陷类的sql
/home/mc5k/projects/adc5000/v50006000/MS_ADC/builds/V5000_6000/generate_lab_class/lab_class_generated.sql

```



### 3、启动 redis_rabbitmq_minio 容器

```shell
# 启动容器
docker run -itd --name redis_mq_minio -p 9379:6379 -p 9900:9000 -p 60006:60006 -p 6672:5672 -p 45672:15672 -v /mydocker/minio/data:/data/minio -v /mydocker/minio/logs:/var/log/minio njllljhfh/componet_server:redis_mq_minio

# 进入 redis_mq_minio 容器
docker exec -it redis_mq_minio /bin/bash

# 进入容器后执行以下操作
# 添加用户 
sudo rabbitmqctl add_user admin admin

# 设置用户权限 
sudo rabbitmqctl set_permissions -p / admin '.*' '.*' '.*'
sudo rabbitmqctl set_user_tags admin administrator

# 添加VHOST 
sudo rabbitmqctl add_vhost adcvhost

# 添加VHOST权限 
sudo rabbitmqctl set_permissions -p adcvhost admin ".*" ".*" ".*"

# 退出容器
exit
```



### 4、启动 nginx 容器

```shell
# 启动容器
docker run -d --name nginx_server -p 8080:80 -p 7156:8156 -p 7756:7756 -v /mydocker/nginx/logs:/var/log/nginx -v /home/mc5k/projects/adc5000/v50006000/html:/projects/adc5000/v50006000/html njllljhfh/nginx:my_nginx

# 进入 nginx_server 容器
docker exec -it nginx_server /bin/bash

# 修改 adc 服务相的ip 为宿主机ip
vim /etc/nginx/conf.d/adc_v50006000.conf 

# 重新加载nginx
nginx -s reload
```



### 5、启动 django_uwsgi 容器

```shell
# 将前端静态文件拷贝到宿主机目录
/home/mc5k/projects/adc5000/v50006000/html

# 拉取项目
cd /home/mc5k/projects/adc5000/v50006000/
git clone git@192.168.10.30:dev/MS_ADC.git
cd MS_ADC
git checkout dev_1.1.9_6000
chmod 777 auto_start_uwsgi_celery.sh start_celery.sh start_uwsgi.sh
cp uwsgi.ini_docker uwsgi.ini
cd /home/mc5k/projects/adc5000/v50006000/MS_ADC/config/settings
cp production_docker.py production.py


# 修改宿主机项目目录中 production.py 文件
vim /home/mc5k/projects/adc5000/v50006000/MS_ADC/config/settings/production.py
# 修改ip
HOST_IP="宿主机ip"
# 修改rabbitmq端口
BROKER_URL = f"amqp://admin:admin@{HOST_IP}:6672/adcvhost"


# 启动容器
docker run -itd --name django_uwsgi -p 7056:8000 -v /home/mc5k/projects/adc5000/v50006000/MS_ADC:/projects/MS_ADC -v /mydocker/uwsgi/logs:/var/log/uwsgi -v /mydocker/celery/logs:/var/log/celery njllljhfh/adc_server:django_py3.8_uwsgi
```



### 6、至此，服务已部署好

```shell
# 用 谷歌浏览器 访问 adc服务器 ip:7156
例如访问 10.0.2.20:7156


# 如有报错，解决问题
# 然后重启 celery，重启 uwsgi
```





## 二、重启 uwsgi 用的脚本 start_uwsgi.sh 

```shell
# 如果修改了adc后端服务的配置，用以下命令重启uwsgi服务
# 在宿主机，重启 django_uwsgi 容器中的 uwsgi服务。（/projects/MS_ADC/start_uwsgi.sh 是容器内的路径）
docker exec django_uwsgi /projects/MS_ADC/start_uwsgi.sh

```





## 三、常用命令

```shell
# 进入 mysql 容器
docker exec -it mysqldb /bin/bash

# 进入 redis_mq_minio 容器
docker exec -it redis_mq_minio /bin/bash

# 进入 nginx_server 容器
docker exec -it nginx_server /bin/bash

# 进入 django_uwsgi 容器
docker exec -it django_uwsgi /bin/bash

# 在宿主机，重启 django_uwsgi 容器中的 uwsgi服务（/projects/MS_ADC/start_uwsgi.sh 是容器内的路径）
docker exec django_uwsgi /projects/MS_ADC/start_uwsgi.sh

# 在宿主机，重启 django_uwsgi 容器中的 celery（/projects/MS_ADC/start_celery.sh 是容器内的路径）
docker exec django_uwsgi /projects/MS_ADC/start_celery.sh

# 进入nginx容器，重新加载nginx
nginx -s reload
```





## 备用

### rabbitmq

/etc/rabbitmq/rabbitmq.config，修改port

```shell
[
{
  rabbit,
  [{
    tcp_listeners,[{"0.0.0.0",5672}]
  }]
},
{
  rabbitmq_management,
  [{
     listener,[{port,45672},{ip,"0.0.0.0"},{ssl,false}]
  }]
}].
```

