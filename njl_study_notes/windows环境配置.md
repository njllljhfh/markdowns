## 一、 windows 下 mysql 

### 1、安装步骤参考博客

[安装步骤参考博客]: https://blog.csdn.net/believesoul/article/details/79323530

[下载地址]: https://dev.mysql.com/downloads/windows/installer/5.7.html



```python
# root账号
root账号：root
root密码：mysql

# 用户账号
账号：matrixtime
密码：matrixtime



```





## 二、nginx

### 1、安装nginx

[下载地址]: https://nginx.org/en/download.html
[安装参考]: https://blog.csdn.net/zhouzhiwengang/article/details/69055184

windows下，解压后就安装好了

### 2、配置nginx

```python
# 重启nginx
nginx -s reload
```





## 三、在虚拟环境下  安装项目环境需要的安装包

1、切换到要创建虚拟环境的根目录下  例如 C:\dev\mvp-python

2、在该目录下 创建虚拟环境  

```
virtualenv -p C:\python\python36\python3.exe venv
```

3、进入虚拟环境的Scripts目录下     C:\dev\mvp-python\venv\Scripts 

4、激活虚拟环境  activate.bat

5、在虚拟环境下批量安装 项目需要的包

```python
pip install -r requirements.txt
```





## 四、安装egg包

```python
# 安装egg包
easy_install ~/Desktop/ImageAlgrithm-1.0-py3.6.egg 

# 卸载egg包
# 注意：执行完下面的卸载命令后，要在项目的环境中，手动的将该包的文件夹删除。
easy_install -m ImageAlgrithm

```