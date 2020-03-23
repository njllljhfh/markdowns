

# 一、常用命令

- 镜像导入、导出

```
导出镜像
语法：docker save -o [导出后的名字] [要导出的镜像名]
例子：docker save -o nginx.tar sswang-nginx

导入镜像
语法：docker load < [image.tar_name]
例子：docker load < nginx.tar
```

- 基于容器创建镜像

```
语法：docker commit -m '改动信息' -a "作者信息" [container_id] [new_image:tag]
例子：docker commit -m 'mkdir /njl' -a 'njl' 1548b36e425a njl-nginx:v0.1
```

- 搜索镜像

```
语法：docker search [image_name]
列子：docker search ubuntu
```

- 获取镜像

```
语法：docker pull [image_name]
例子：docker pull nginx
```

- 查看容器网络信息

```
语法：docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [container_id]
例子：docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' 0958609fc9ea
```

- 查看容器端口信息

```
语法：docker port [容器id]
例子：docker port 930f29ccdf8a
```

- 删除全部容器

```
docker rm -f $(docker ps -a -q)
```

- 删除全部镜像

```
docker rmi -f $(docker image ls -a -q)
```

- 从宿主机拷贝文件到容器

```
语法：docker cp 宿主机中要拷贝的文件名及其路径 容器名:要拷贝到容器里面对应的路径
例子：docker cp /Users/njl/Documents/docker_study/jflg/ ubuntu1604_1:/jflg
```

- 从容器拷贝文件到宿主机

```
docker cp 容器名:容器中要拷贝的文件名及其路径 要拷贝到宿主机里面对应的路径
```

- 进入正在运行的容器

```
语法：docker exec -it 容器id /bin/bash
例子：docker exec -it d4c1fbe8b89b /bin/bash
```

- 用命令行连接django容器和mysql容器

- 查看容器的挂载信息

```
.Source：宿主机目录
.Destination：容器内的目录
语法：docker inspect --format '{{range .Mounts}} {{.Source}} --- {{.Destination}} {{end}}' [container_id]
例子：docker inspect --format '{{range .Mounts}} {{.Source}} --- {{.Destination}} {{end}}' 04078c5e4a38
```



# 二、Dockerfile

## 2.1、基础指令

- FROM

- MAINTAINER

- RUN

```
格式:
	RUN <command>											(shell 模式) 
	RUN["executable", "param1", "param2"]。					(exec 模式)
解释:	
	表示当前镜像构建时候运行的命令，如果有确认输入的话，一定要在命令中添加 -y
	如果命令较长，那么可以在命令结尾使用 \ 来换行
	生产中，推荐使用上面数组的格式
注释:
	shell模式:类似于 /bin/bash-ccommand
		举例: RUN echo hello
	exec 模式:类似于 RUN ["/bin/bash", "-c", "command"]
		举例: RUN ["echo", "hello"]
```

- EXPOSE

## 2.2、容器运行时指令

- CDM

```
格式:
	CMD ["executable","param1","param2"]		(exec 模式)推荐
	CMD command param1 param2					(exec 模式)推荐
	CMD ["param1","param2"]						提供给 ENTRYPOINT 的默认参数;
解释:
	CMD 指定容器启动时默认执行的命令
	每个 Dockerfile 只运行一条 CMD 命令，如果指定了多条，只有最后一条会被执行
	如果你在启动容器的时候使用 docker run 指定的运行命令，那么会覆盖 CMD 命令。 
	举例: CMD ["/usr/sbin/nginx","-g","daemon off"]
```

- ENTRYPOINT

```
格式:
	ENTRYPOINT ["executable", "param1","param2"] 		(exec 模式) 
	ENTRYPOINT command param1 param2 					(shell 模式)
解释:
	和 CMD 类似都是配置容器启动后执行的命令，并且不会被 docker run 提供的参数覆盖。
	每个 Dockerfile 中只能有一个 ENTRYPOINT，当指定多个时，只有最后一个起效。
	生产中我们可以同时使用 ENTRYPOINT 和 CMD，
	想要在dockerrun时被覆盖，可以使用 "dockerrun--entrypoint"
```

2.3、文件编辑指令

- ADD

- COPY
- VOLUME



# 三、实践

## 3.1、在命令行连接django容器和mysql容器

### 3.1.1、先启动mysql容器、启动时要挂载数据库文件

```python
docker run -d -p 4306:3306 --privileged=true -v /Users/njl/Documents/docker_study/mysql/conf/my.cnf:/etc/mysql/my.cnf -v /Users/njl/Documents/docker_study/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=mysql --name mysql_njl mysql:5.7
```



### 3.1.2、再启动django容器、启动时连接mysql

```python
docker run -itd --name django_uwsgi_py36 --link mysql_njl:dbmysql -p 8181:8000 -p 8989:8989 django_uwsgi_py36:v1.0
```

3.1.2.2

```shell
docker run -itd --name django_uwsgi_py36_mvp20 --link mysql_njl:dbmysql -p 8171:8000 -p 8979:8989 -v /Users/njl/Documents/MTWork/GitLab/mvp-python:/projects/mvp20 django_uwsgi_py36:v1.0
```



### 3.1.3、进入到django容器中、修改setting.py文件中的myslq配置

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mvp',
        'USER': 'root',
        'PASSWORD': os.environ.get('DBMYSQL_ENV_MYSQL_ROOT_PASSWORD'), # 获取环境变量
        'HOST': os.environ.get('DBMYSQL_PORT_3306_TCP_ADDR'), # 获取环境变量
        'PORT': '3306',
    }
}

# DBMYSQL_ 这个前缀是在启动django容器的时，给mysql容器起的别名   mysql_njl:dbmysql
```

### 3.1.4、启动Nginx容器、连接到django的容器、挂在宿主机修改好的配置到nginx容器中

- --link django_uwsgi_py36_8989

```python
docker run -d --name nginx_njl --link django_uwsgi_py36_8989 -p 80:80 -p 8005:8000 -v /Users/njl/Documents/docker_study/nginx/logs:/var/log/nginx -v /Users/njl/Documents/docker_study/nginx/nginx:/etc/nginx nginx
```

此时在django容器中用uwsgi启动项目，可以在宿主机用127.0.0.1:8005/api/JFLG/….来访问django的动态逻辑

