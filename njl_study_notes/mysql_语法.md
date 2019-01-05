#### 1、数据库的<备份> 与 <恢复>

####  

```mysql
# 数据库备份
# 在终端输入
---语法：mysqldump -uroot -p 要备份的数据库名称 > 指定路径/xxx.sql
mysqldump -uroot -p mvp > /home/njl/Desktop/mvp_20180730.sql

# 数据库恢复
#-->1.在mysql数据库中，创建数据库
---语法: create database 新数据库名称 charset=utf8;
create database mvp charset=uff8;

#-->2.在终端输入
---语法：mysql -uroot -p 新数据库名称 < 指定路径/xxx.sql
mysql -uroot -p mvp < /home/njl/Desktop/mvp_20180721.sql

# 备份一张表的数据
mysqldump -u root -p mvp_server SYS_FUNCTION > sys_func_db.sql 

# 清除表中数据
delete from DL_INTERFACE;

```

