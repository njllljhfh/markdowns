## centos7 配置固定ip

1. 设置ip、子网掩码、网关、DNS

    ```bash
    vim /etc/sysconfig/network-scripts/ifcfg-你的网卡名字
    
    BOOTPROTO=static
    # 下面的值，根据自己的网络环境来设置
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

