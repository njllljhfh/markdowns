# mysql创建用户及授权





### 登录MySQL

```undefined
mysql -u root -p
```



### 添加新用户

- 允许本地 IP 访问 localhost, 127.0.0.1

```sql
create user 'njl'@'localhost' identified by '123456';
```



- 允许外网 IP 访问

```sql
create user 'njl'@'%' identified by '123456';
```



- 刷新授权

```sql
flush privileges;
```



### 为用户创建数据库

```sql
create database test DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
```



### 为新用户分配权限

- 授予用户通过外网IP对于该数据库的全部权限

```sql
grant all privileges on `mvp_server`.* to 'njl'@'%' identified by '123456';
```



- 授予用户在本地服务器对该数据库的全部权限

```sql
grant all privileges on `mvp_server`.* to 'njl'@'localhost' identified by '123456';
```



- 刷新权限

```sql
flush privileges;
```



- 退出 root 重新登录

```sql
exit
```



- 用新帐号 test 重新登录，由于使用的是 % 任意IP连接，所以需要指定外部访问IP

```css
mysql -u njl -h 115.28.203.224 -p
```



- 在Ubuntu服务器下，MySQL默认是只允许本地登录，因此需要修改配置文件将地址绑定给注释掉：

```vbscript
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
# bind-address		= 127.0.0.1		#注释掉这一行就可以远程登录了
```



- 不然会报如下错误：

ERROR 2003 (HY000): Can't connect to MySQL server on 'host' (111)