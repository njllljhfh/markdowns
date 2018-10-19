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


# 清除表中数据
delete from DL_INTERFACE;
delete from DL_MODEL_INFO;
delete from DL_PARAM_INFO;
delete from DL_REQ_PARAM;
delete from IMG_INFO;
delete from IMG_INS_INFO;
delete from IMG_LAB_INFO;
delete from IMG_LAB_RESULT;
delete from LAB_CLASS;
delete from OPENCV_INTERFACE;
delete from SET_TYPE;
delete from SYS_FUNCTION;
delete from SYS_ORG;
delete from SYS_ROLE;
delete from SYS_USER;
delete from SYS_USER_GROUPS;
delete from SYS_USER_ROLE_REL;
delete from TASK_CLASS_REL;
delete from TASK_IMG_REL;
delete from TASK_MODEL_REL;
delete from USER_GROUP_REL;
delete from USER_TASK;

delete from TASK_TYPE;
```

