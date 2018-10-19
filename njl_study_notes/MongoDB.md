## 1 、安装mongoDB

[ubuntu安装mongodb 4.0]: https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/

## 2、mongodb启动与关闭，以及连接mongodb

```python
# mongodb服务器启动文件 的 位置 
/usr/bin/mongod

# 启动mongodb客户端 的 位置
/usr/bin/mongo

# 数据文件位置
/data/db

# MongoDB默认数据文件路径
/var/lib/mongodb/

# MongoDB默认日志文件路径
/var/log/mongodb/mongodb.log

# 启动mongodb服务器
sudo service mongod start

# 关闭mongodb服务器
sudo service mongod stop

# 重启mongodb服务器
sudo service mongod restart

# 连接mongodb服务器
sudo mongo --host 127.0.0.1:27017

```

## 3、常用操作

```python
# 查看mongodb服务器的连接数
db.serverStatus().connections

# 显示当前数据库 名字
db

# 查看当前数据库中，所有 collection 的名（需要先 use 该数据库）
use mvp_dl
db.getCollectionNames()

# 查看数据库名字为 mvp_dl 的状态
use mvp_dl
db.stats()


# 查看数据库中名为 IMG.files 的 collection 的状态
use mvp_dl
db.IMG.files.stats()  # 会显示 该 collection 的全部状态信息

# 会显示名为 IMG.files 的 collection 中 记录文档的个数
use mvp_dl
db.IMG.files.stats().count

# 用 mongo_id, 查询 mvp_dl 数据库中名字为 IMG.files 的 collection中的数据。pretty()方法用来格式化
use mvp_dl
db.IMG.files.find({"_id":ObjectId("5b4c3e59e63610452fd22cc1")}).pretty()

# 用 mongo_id, 查询 mvp_dl 数据库中名字为 JSON.files 的 collection中的数据
use mvp_dl
db.JSON.files.find({"_id":ObjectId("5b497418e63610bddb502c42")}).pretty()

# 查询 mvp_dl 数据库中名字为 JSON.files 的 collection 中的 全部数据
use mvp_dl
db.IMG.files.find().pretty()

# 查询 mvp_dl 数据库中名字为 JSON.files 的 collection 中的 全部数据
use mvp_dl
db.JSON.files.find().pretty()
```



## 4、mongodb **连接池的重要参数** 

内置连接池有多个重要参数，分别是：

- connectionsPerHost：每个主机的连接数，默认是10
- threadsAllowedToBlockForConnectionMultiplier：线程队列数，它以上面 connectionsPerHost值相乘的结果就是线程队列最大值。如果连接线程排满了队列就会抛出“Out of semaphores to get db”错误，默认是5
- maxWaitTime：最大等待连接的线程阻塞时间
- connectTimeout：连接超时的毫秒。0是默认和无限
- socketTimeout：socket超时。0是默认和无限
- autoConnectRetry：这个控制是否在一个连接时，系统会自动重试

