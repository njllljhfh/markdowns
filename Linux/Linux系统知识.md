## centos7 配置固定ip

1. 设置ip、子网掩码、网关、DNS

    ```bash
    vim /etc/sysconfig/network-scripts/ifcfg-你的网卡名字
    
    # 修改已有配置
    BOOTPROTO=static
    # 添加下面配置，下面的值，根据自己的网络环境来设置
    IPADDR=192.168.0.110
    NETMASK=255.255.255.0  # 如果是虚拟机，跟物理主机相同，否则会连不上互联网。
    GATEWAY=192.168.0.1  # 设为跟物理主机相同
    DNS1=192.168.0.1  # 我设置为与 GATEWAY 相同
    ```

2. 设置网关

    ```bash
    vim /etc/sysconfig/network
    GATEWAY=192.168.0.1
    ```

3. 重启网络

    ```bash
    service network restart
    ```



---



## centos7 设置定时任务

1. 在 `/etc/crontab`  文件中添加定时任务

    ```shell
    vim /etc/crontab 
     
    SHELL=/bin/bash
    PATH=/sbin:/bin:/usr/sbin:/usr/bin
    MAILTO=root
    
    # For details see man 4 crontabs
    
    # Example of job definition:
    # .---------------- minute (0 - 59)
    # |  .------------- hour (0 - 23)
    # |  |  .---------- day of month (1 - 31)
    # |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
    # |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
    # |  |  |  |  |
    # *  *  *  *  * user-name  command to be executed
    1 * * * * /njl/test_crontab/current_time.sh  # 自定义的脚本
    ```

2. 刷新定时任务，使新添加的定时任务生效。

    ```shell
    crontab /etc/crontab
    ```



---



## centos7 安装 python3.6.5

1. 首先安装依赖包，centos里面是-devel，如果在ubuntu下安装则要改成-dev，依赖包缺一不可

    ```bash
    sudo yum -y groupinstall "Development tools"
    
    sudo yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel
    
    # 如果上面的命令执行失败报错错：No more mirrors to try.
    # 先按顺序执行下面的命令，更新yum，然后再执行上面依赖包的安装命令。
    yum clean all
    yum makecache
    yum -y update
    ```

2. 然后获取 `python3.6.5` 的安装包

    ```bash
    wget https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tar.xz
    ```

3. 解压 `python3.6.5` 安装包

    ```bash
    tar -xvJf  Python-3.6.5.tar.xz
    ```

4. 创建 `python3.6.5` 的保存文件夹，我的存放目录是 `/usr/local/bin/python3.6`

    ```bash
    mkdir /usr/local/bin/python3.6
    ```

5. 配置 `python3.6.5` 的安装目录并安装

    ```bash
    cd Python-3.6.5
    ./configure --prefix=/usr/local/bin/python3.6
    sudo make
    sudo make install
    ```

6. 创建软链接

    ```bash
    ln -s /usr/local/bin/python3.6/bin/python3 /usr/bin/python3
    ln -s /usr/local/bin/python3.6/bin/pip3 /usr/bin/pip3
    ```

7. 最后在命令行中输入python3，能够进入python3终端即成功安装

    ```shell
    # 在命令行输入：python3
    
    # 成功进入python3
    Python 3.6.5 (default, Feb  8 2021, 14:54:52) 
    [GCC 4.8.5 20150623 (Red Hat 4.8.5-44)] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> 
    ```

    

---



## centos7 安装 mysql5.7

1. 由于CentOS7的yum源中没有mysql，需要到mysql的官网下载yum repo配置文件：

    ```shell
    # 注意：wget 会将要下载的文件保存在当前所在的文件目录中。
    # 下载命令：
    wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
    ```

2. 然后进行yum源的安装：

    ```shell
    rpm -ivh mysql57-community-release-el7-9.noarch.rpm
    ```

3. 安装完成后，就可以使用yum命令安装mysql了：

    ```shell
    yum -y install mysql-server
    ```

4. 启动mysql：

    ```shell
    systemctl start mysqld
    ```

5. 查看mysql状态：

    ```shell
    systemctl status mysqld
    ```

6. 获取mysql的临时密码：

    ```shell
    # 执行下面的命令
    grep 'temporary password' /var/log/mysqld.log
    
    # 得到输出结果如下（其中 '4ZdPN,)d.wZ9' 就是root用户的临时密码）：
    2021-02-09T07:10:12.947959Z 1 [Note] A temporary password is generated for root@localhost: 4ZdPN,)d.wZ9
    ```

7. 登录mysql：（密码为上一步骤获取的临时密码）

    ```shell
    mysql -u root -p(此处不用输入密码，按下回车后会专门要你再输入密码的)
    ```

8. 登录成功后，做任何操作都会被要求先修改密码。请注意：如果修改的密码太过简单，依然会提示error，修改失败。因为5.7及以上版本的数据库对密码做了强度要求，默认密码的要求必须是大小写字母数字特殊字母的组合且至少要8位长度。

    ```mysql
    ALTER USER 'root'@'localhost' IDENTIFIED BY 'Mysql-123456';
    ```

9. 至此mysql5.7安装结束。



---



## Ubuntu18.04 设置固定ip

[参考文档](https://blog.csdn.net/weixin_43843847/article/details/90938308)

1. VMware虚拟网络配置

![选择虚拟网络编辑器](.\image\选择虚拟网络编辑器.png)



![选择虚拟网络编辑器2](.\image\选择虚拟网络编辑器2.png)



2. 选择宿主机用于上网的网络（可在宿主机的网络连接配置中查看）

![选择虚拟网络编辑器4](.\image\选择虚拟网络编辑器4.png)



![选择虚拟网络编辑器3](.\image\选择虚拟网络编辑器3.png)



3. 设置桥接模式

![设置桥接模式](.\image\设置桥接模式.png)



![设置桥接模式2](.\image\设置桥接模式2.png)

4. 进入虚拟机系统

5. 查看网卡，找到要设置固定ip的网卡

    ```
    ifconfig
    ```

6. 设置名为 ens33 网卡的ip

    ```shell
    # 用vim打开网络配置文件
    sudo vim /etc/netplan/01-network-manager-all.yaml
    
    # 修改前
      1 # Let NetworkManager manage all devices on this system
      2 network:
      3   version: 2
      4   renderer: NetworkManager
    
     
    # 修改后
      1 # Let NetworkManager manage all devices on this system
      2 network:
      3   version: 2
      4   #renderer: NetworkManager
      5   ethernets:
      6     ens33:
      7       addresses: [10.0.1.110/22]  # 固定ip/子网掩码（与宿主机保持一致,22表示255.255.252.0）
      8       gateway4: 10.0.0.1  # 网关（与宿主机保持一致）
      9       dhcp4: no  # no代表不是用dhcp动态获取ip，yes代表使用dhcp动态获取ip
     10       nameservers:
     11         addresses: [8.8.8.8]  # DNS
     12         #search: [localdomin]  # 虚拟机所在的domain（这项不用貌似也可以）
     
    ```

7. 重启网络

    ```shell
    sudo netplan apply
    ```



---

## linux命令

### Linux 在后台以守护进程运行 python 脚本，并将日志重定向到指定文件

```shell
# 以守护进程方式运行 image_collector_atesi.py 并将日志重定向到 image_collector.log文件（-s和-o是此python脚本需要的参数）
nohup python3 -u image_collector_atesi.py -s /APP/testimg/ -o ./image_collector_atesi_vi_export >> image_collector.log 2>&1 &
```



---



## Ubuntu18.04 安装python虚拟环境

[参考文档](https://blog.csdn.net/focusdroid/article/details/93484175)

1. 安装虚拟环境

   ```shell
   sudo pip3 install virtualenv
   ```

2. 安装虚拟环境扩展包

   ```shell
   sudo pip3 install virtualenvwrapper
   ```

3. 编辑home目录下的 `.bashrc` 文件，添加下面两行代码

   ```shell
   export WORKON_HOME=$HOME/.virtualenvs
   export VIRTUALENVWRAPPER_PYTHON='/usr/bin/python3.6'
   source /usr/local/bin/virtualenvwrapper.sh
   ```

4. 保存并退出，执行以下命令

   ```shell
   # 注意此时是在 home 目录下
   source .bashrc
   ```

5. 创建虚拟环境

   ```shell
   #1 创建虚拟环境命令(python2的虚拟环境)
   mkvirtualenv 虚拟环境名
   
   #2 创建python3的虚拟环境
   mkvirtualenv -p python3 虚拟环境的名字
   
   #3 进入虚拟环境
   workon 虚拟环境名
   
   #4 退出虚拟环境
   deactivate
   
   #5 查看机器上有多少虚拟环境
   workon 空格 + 两个Tab键
   
   #6 删除虚拟环境
   rmvirtualenv 虚拟环境名称
   
   #7 查看虚拟环境装了那些包
   pip list 
   ```

   



---





## Ubuntu18.04 安装 MySQL 

[官方文档](https://dev.mysql.com/doc/mysql-apt-repo-quick-guide/en/#apt-repo-fresh-install)

1. 下载  APT仓库包 到当前目录下

   ```shell
   wget https://dev.mysql.com/get/mysql-apt-config_0.8.17-1_all.deb
   ```

2. 安装刚才下载的  APT仓库包 

   ```shell
   sudo dpkg -i mysql-apt-config_0.8.17-1_all.deb
   ```

3. 更新新安装包信息从 MySQL APT repository

   ```shell
   sudo apt-get update
   ```

4. 用 APT 安装 MySQL 

   ```shell
   sudo apt-get install mysql-server
   ```

5. 配置可远程访问的账户

   - 用安装时 mysql 的密码，控制台登陆 mysql

   ```shell
   mysql -uroot -p
   输入密码登陆，之后进行用户配置
   ```
   
   - 使用数据库
   
   ```mysql
   use mysql
   ```
   
   - 配置授权用户（注意这里的 用户名、密码，必须是自己要设置的）
   
   ```mysql
   # 这里，我们在 <GRANT> 语句内设置的用户名和密码，就是远程访问的用户和密码
   GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'mysql' WITH GRANT OPTION;
   ```
   
   - 刷新权限
   
   ```mysql
   FLUSH PRIVILEGES;
   ```
   
6. 设置开启远程登录

   - 进入配置文件

   ```shell
   sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
   ```

   - 注释掉下图中的内容，保存并退出配置文件

   ![mysql配置远程访问](.\image\mysql配置远程访问.png)

   - 重启 mysql

   ```shell
   sudo systemctl restart mysql
   ```

   

---



## Ubuntu18.04 安装 zabbix4.0

邮箱配置：[参考链接](https://blog.csdn.net/abcdu1/article/details/89850898)

- 第三方授权码，在浏览器登录邮件，然后获取。

![zabbix邮箱配置](.\image\zabbix邮箱配置.png)



---



## Ubuntu18.04 查看端口占用

```shell
sudo netstat -anp |grep 3306
```



## 根据名称查看进程号，并 kill

```shell
ps -ef | grep uwsgi | grep -v grep | awk '{print $2}' | xargs kill -9
```



---

## Anaconda

### Ubuntu18.04 安装 Anaconda

[Anaconda安装包下载地址](https://repo.anaconda.com/archive)

```shell
# 下载命令
wget https://repo.anaconda.com/archive/Anaconda3-5.3.1-Linux-x86_64.sh

# bash 命令安装：
cd /home/njl/Downloads # cd 到你下载的Anaconda目录
sudo bash Anaconda3-XXX.sh # bash 对安装包进行安装

# 安装完成后执行如下命令，使得anaconda的配置生效
source ~/.bashrc

#安装完成测试：
可以使用命令anaconda -V 或者 conda --version 查看conda版本，同时也可验证是否安装完成。

#注: 在第二步的安装过程中会问你是否接受授权，输入yes，会让你选择安装目录，最好用默认的。如果直接回车或者选择输入no，安装完成后输入anaconda -V 会提示找不到命令，因为你还需将安装目录导入系统环境中，可以使用以下命令：
export PATH=/path/install/anaconda3/bin:$PATH  # 把anaconda安装的bin目录写入配置文件
```



### Anaconda安装虚拟python的虚拟环境

```shell
#创建虚拟环境
conda create -n your_env_name python=x.x

# 删除虚拟环境
conda remove -n your_env_name --all

# 切换conda环境
conda activate env_name
 
# 退出conda环境
conda deactivate

# 查看已安装的环境
conda info --envs
```




---

