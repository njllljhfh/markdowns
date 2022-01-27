# rabbitmq 安装（ubuntu1804）

```shell
echo -e "\033[46m ##############################----开始安装RABBITMQ----#########################\033[0m]"
echo -e "\033[45m @@@@@----下载ERLANG-NOX----@@@@@\033[0m]"

sudo apt-get install erlang-nox

sudo rm /var/lib/dpkg/lock-frontend

sudo rm /var/lib/dpkg/lock

sudo apt-get update

echo -e "\033[45m @@@@@----下载RABBITMQ-SERVER----@@@@@\033[0m]"
sudo apt-get install rabbitmq-server

echo -e "\033[45m @@@@@----打开RABBITMQ管理工具----@@@@@\033[0m]"
sudo rabbitmq-plugins enable rabbitmq_management

echo -e "\033[45m @@@@@----添加用户----@@@@@*"
sudo rabbitmqctl add_user admin admin

echo -e "\033[45m @@@@@----设置用户权限----@@@@@\033[0m]"
sudo rabbitmqctl set_permissions -p / admin '.*' '.*' '.*'
sudo rabbitmqctl set_user_tags admin administrator

# 以下 VHOST 根据需求添加
echo -e "\033[45m @@@@@----添加VHOST----@@@@@\033[0m]"
sudo rabbitmqctl add_vhost adcvhost

echo -e "\033[45m @@@@@----添加VHOST权限----@@@@@\033[0m]"
sudo rabbitmqctl set_permissions -p adcvhost admin ".*" ".*" ".*"

echo -e "\033[45m @@@@@----重启RABBITMQ-SERVER----@@@@@\033[0m]"
systemctl restart rabbitmq-server

echo -e "\033[46m ##############################----RABBITMQ安装完成----###########################\033[0m]"

```





# rabbitmq 监控页面

```shell
# 用admin账号登录
# 监控地址
ip:15672
```





# rabbitmq集群部署

[参考连接](https://www.cnblogs.com/sellsa/p/8056173.html)



## 主机名更改

- RabbitMQ 节点使用主机名相互通信。因此，所有节点名称必须能够解析集群中所有的节点名称。像 rabbitmqctl 这样的工具也是如此
- 除此之外，默认情况下RabbitMQ使用系统的当前主机名来命名数据库目录。如果主机名更改，则会创建一个新的空数据库。为了避免数据丢失，建立一个固定和可解析的主机名至关重要。每当主机名更改时，您应该重新启动RabbitMQ
- 如果要使用节点名称的完整主机名（RabbitMQ默认为短名称），并且可以使用DNS解析完整的主机名，则可能需要调查设置环境变量 RABBITMQ_USE_LONGNAME = true



## 配置节点

### 绑定hosts文件

```shell
# 绑定各个节点主机ip
# node1、node2、node3是三个主机的名称
# 主机的名称在 /etc/hostname 中，可根据需要修改，修改后重启服务器

# 用vim打开 /etc/hosts
sudo vim /etc/hosts
# 添加以下内容，保存并退出(ip换成自己节点的ip)
192.168.88.1 node1
192.168.88.2 node2
192.168.88.3 node3
```



### 设置节点互相验证：Erlang Cookie

```
RabbitMQ节点和CLI工具（例如rabbitmqctl）使用cookie来确定它们是否被允许相互通信，要使两个节点能够通信，它们必须具有相同的共享密钥，称为Erlang Cookie.
Cookie只是一个字符串，最多可以有255个字符。它通常存储在本地文件中。该文件必须只能由所有者访问（400权限）。每个集群节点必须具有相同的 cookie，文件位置/var/lib/rabbitmq/.erlang.cookie， 把rabbit2、rabbit3设置成和rabbit2一样的即可，权限是400

修改后重启rabbitmq
```



### 用cluster_status命令查看集群状态

现在启动了三个独立的RabbitMQ,我们用cluster_status命令查看集群状态

```shell
# 查看集群状态
sudo rabbitmqctl cluster_status

# 节点node1
njl@node1:~$ sudo rabbitmqctl cluster_status
Cluster status of node rabbit@node1
[{nodes,[{disc,[rabbit@node1]}]},
 {running_nodes,[rabbit@node1]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node1,[]}]}]

# 节点node2
njl@node2:~$ sudo rabbitmqctl cluster_status
Cluster status of node rabbit@node2
[{nodes,[{disc,[rabbit@node2]}]},
 {running_nodes,[rabbit@node2]},
 {cluster_name,<<"rabbit@node2">>},
 {partitions,[]},
 {alarms,[{rabbit@node2,[]}]}]

# 节点node3
njl@node3:~$ sudo rabbitmqctl cluster_status
Cluster status of node rabbit@node3
[{nodes,[{disc,[rabbit@node3]}]},
 {running_nodes,[rabbit@node3]},
 {cluster_name,<<"rabbit@node3">>},
 {partitions,[]},
 {alarms,[{rabbit@node3,[]}]}]
```



## 添加节点

### 连接集群中的节点

为了连接集群中的三个节点，我们把 **rabbit@node2** 和 **rabbit@node3** 节点加入到 **rabbit@node1** 节点集群

在 rabbit@node2 的服务器上执行以下命令：

```shell
# 停止 rabbitmq 应用，注意这里停止的是 rabbitmq应用 不是 rabbitmq 服务（Stopping rabbit application）
[root@node2:~]# rabbitmqctl stop_app

# 将 rabbit@node2 加入集群
[root@node2:~]# rabbitmqctl join_cluster rabbit@node1
Clustering node rabbit@node2 with rabbit@node1  # 成功后，输出的结果

# 启动 rabbitmq 应用
[root@node2:~]# rabbitmqctl start_app
```



现在我们在node1 、node2 任意一个节点上查看集群状态，我们可以看到这两个节点加入了一个集群

```shell
# 在node1、node2上执行
sudo rabbitmqctl cluster_status

# node1输出结果
[root@node1:~]# sudo rabbitmqctl cluster_status
Cluster status of node rabbit@node1
[{nodes,[{disc,[rabbit@node1,rabbit@node2]}]},
 {running_nodes,[rabbit@node2,rabbit@node1]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node2,[]},{rabbit@node1,[]}]}]
 
# node2输出结果
[root@node2:~]# sudo rabbitmqctl cluster_status
Cluster status of node rabbit@node2
[{nodes,[{disc,[rabbit@node1,rabbit@node2]}]},
 {running_nodes,[rabbit@node1,rabbit@node2]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node1,[]},{rabbit@node2,[]}]}]
```



重复上述操作，将 rabbit@node3 加入集群

```shell
# 停止 rabbitmq 应用，注意这里停止的是 rabbitmq应用 不是 rabbitmq 服务（Stopping rabbit application）
[root@node3:~]# rabbitmqctl stop_app

# 将 rabbit@node3 加入集群
[root@node3:~]# rabbitmqctl join_cluster rabbit@node1
Clustering node rabbit@node3 with rabbit@node1  # 成功后，输出的结果

# 启动 rabbitmq 应用
[root@node3:~]# rabbitmqctl start_app
```



现在集群中有三个节点

```shell
[root@node3:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node3
[{nodes,[{disc,[rabbit@node1,rabbit@node2,rabbit@node3]}]},
 {running_nodes,[rabbit@node1,rabbit@node2,rabbit@node3]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node1,[]},{rabbit@node2,[]},{rabbit@node3,[]}]}]
```



- 通过遵循上述步骤，我们可以在集群正在运行的同时随时向集群添加新节点

- 已加入群集的节点可随时停止。他们也可以崩溃。在这两种情况下，群集的其余部分都会继续运行，并且节点在再次启动时会自动“跟上”（同步）其他群集节点。



### 测试

我们关闭 rabbit@node1 和 rabbit@node3，并检查每一步中的集群状态

```shell
# 停止 node1 的 rabbitmq 
[root@node1:~]# rabbitmqctl stop
Stopping and halting node rabbit@node1

# 在 node2 上查看集群状态
[root@node2:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node2
[{nodes,[{disc,[rabbit@node1,rabbit@node2,rabbit@node3]}]},
 {running_nodes,[rabbit@node3,rabbit@node2]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node3,[]},{rabbit@node2,[]}]}]

# 在 node3 上查看集群状态
[root@node3:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node3
[{nodes,[{disc,[rabbit@node1,rabbit@node2,rabbit@node3]}]},
 {running_nodes,[rabbit@node2,rabbit@node3]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node2,[]},{rabbit@node3,[]}]}]
 
# 停止 node3 的 rabbitmq 
[root@node3:~]# rabbitmqctl stop
Stopping and halting node rabbit@node3

# 在 node2 上查看集群状态
[root@node2:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node2
[{nodes,[{disc,[rabbit@node1,rabbit@node2,rabbit@node3]}]},
 {running_nodes,[rabbit@node2]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node2,[]}]}]
```



 现在我们再次启动节点，并检查集群状态

```shell
# 启动 node3 的 rabbitmq 
[root@node3:~]# rabbitmq-server -detached

# 在 node3 上查看集群状态
[root@node3:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node3
[{nodes,[{disc,[rabbit@node1,rabbit@node2,rabbit@node3]}]},
 {running_nodes,[rabbit@node2,rabbit@node3]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node2,[]},{rabbit@node3,[]}]}]
 
# 在 node2 上查看集群状态
[root@node2:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node2
[{nodes,[{disc,[rabbit@node1,rabbit@node2,rabbit@node3]}]},
 {running_nodes,[rabbit@node3,rabbit@node2]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node3,[]},{rabbit@node2,[]}]}]

# 启动 node1 的 rabbitmq
[root@node1:~]# rabbitmq-server -detached

# 在 node1 上查看集群状态
[root@node1:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node1
[{nodes,[{disc,[rabbit@node1,rabbit@node2,rabbit@node3]}]},
 {running_nodes,[rabbit@node2,rabbit@node3,rabbit@node1]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node2,[]},{rabbit@node3,[]},{rabbit@node1,[]}]}]

# 在 node2 上查看集群状态
[root@node2:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node2
[{nodes,[{disc,[rabbit@node1,rabbit@node2,rabbit@node3]}]},
 {running_nodes,[rabbit@node1,rabbit@node3,rabbit@node2]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node1,[]},{rabbit@node3,[]},{rabbit@node2,[]}]}]

# 在 node3 上查看集群状态
[root@node2:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node3
[{nodes,[{disc,[rabbit@node1,rabbit@node2,rabbit@node3]}]},
 {running_nodes,[rabbit@node1,rabbit@node2,rabbit@node3]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node1,[]},{rabbit@node2,[]},{rabbit@node3,[]}]}]
```



**一些重要的警告（尚未测试）：**

- 当整个集群关闭时，最后一个关闭的节点必须是第一个要联机的节点。
- 如果要脱机的最后一个节点无法恢复，可以使用forget_cluster_node命令将其从群集中删除
- 如果所有集群节点同时停止并且不受控制（例如断电），则可能会留下所有节点都认为其他节点在其后停止的情况。在这种情况下，您可以在一个节点上使用force_boot命令使其再次可引导



## 集群移除节点

### 将当前节点移除集群

当节点不再是集群的一部分时，需要从集群中明确地删除节点。我们首先从集群中删除 **rabbit@node3**，并将其返回到独立操作

在 rabbit@node3 上：

1. 停止 RabbitMQ 应用程序
2. 重置节点
3. 重新启动 RabbitMQ 应用程序

```shell
# 停止 node3 的 rabbitmq应用
[root@node3:~]# rabbitmqctl stop_app
Stopping rabbit application on node rabbit@node3

# 重置 node3 rabbitmq
[root@node3:~]# rabbitmqctl reset
Resetting node rabbit@node3

# 重新启动 node3 rabbitmq应用
[root@node3:~]# rabbitmqctl start_app
Starting node rabbit@node3


# 在 node3 上运行 cluster_status 命令确认 rabbit@node3 现在不再是集群的一部分并独立运行
[root@node3:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node3
[{nodes,[{disc,[rabbit@node3]}]},
 {running_nodes,[rabbit@node3]},
 {cluster_name,<<"rabbit@node3">>},
 {partitions,[]},
 {alarms,[{rabbit@node3,[]}]}]

# 查看 node1 集群状态，node3已经不在急群中
[root@node1:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node1
[{nodes,[{disc,[rabbit@node1,rabbit@node2]}]},
 {running_nodes,[rabbit@node2,rabbit@node1]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node2,[]},{rabbit@node1,[]}]}]

# 查看 node2 集群状态，node3已经不在急群中
[root@node2:~]# rabbitmqctl cluster_status
Cluster status of node rabbit@node2
[{nodes,[{disc,[rabbit@node1,rabbit@node2]}]},
 {running_nodes,[rabbit@node1,rabbit@node2]},
 {cluster_name,<<"rabbit@node1">>},
 {partitions,[]},
 {alarms,[{rabbit@node1,[]},{rabbit@node2,[]}]}]
```



### 将远程节点移除集群

我们也可以远程删除节点，例如，在处理无响应的节点时，这很有用
比如：我们在节点 **rabbit@node2** 上把 **rabbit@node1** 从集群中移除

```shell
# 停止node1 rabbitmq应用
[root@node1:~]# rabbitmqctl stop_app
Stopping rabbit application on node rabbit@node1

# 我们在节点 node2 上把 node1 从集群中移除
root@node2:/home/njl# rabbitmqctl forget_cluster_node rabbit@node1
Removing node rabbit@node1 from cluster
```

请注意，**node1** 仍然认为它与 **node2** 在一个集群中 ，此时试图启动它将导致错误。

```shell
Error: {error,{inconsistent_cluster,"Node rabbit@node1 thinks it's clustered with node rabbit@node2, but rabbit@node2 disagrees"}}
```

我们需要重置 **node1** 才能重新启动 **node1**。

```shell
# 必须重置
[root@node1:~]# rabbitmqctl reset 
Resetting node rabbit@node1

# 重启 node1 rabbitmq应用
[root@node1:~]# rabbitmqctl start_app
Starting node rabbit@node1
```

现在查看集群状态，三个节点都时作为独立的节点

- 请注意，**rabbit@node2** 保留了集群的剩余状态，而 **rabbit@node1** 和 **rabbit@node3** 是刚刚初始化的RabbitMQ。
- 此时 admin 用户只在 **rabbit@node2** 节点上，**node1** 和 **node3** 上已经没有admin账户了。
- 如果我们想重新初始化 **rabbit@node2** ，需要执行与其他节点相同的上述步骤：

```shell
# 停止 node2 rabbitmq应用
[root@node2:~]# rabbitmqctl stop_app
Stopping rabbit application on node rabbit@node2

# 重置 node2 rabbitmq
[root@node2:~]# rabbitmqctl reset
Resetting node rabbit@node2

# 重新启动 node2 rabbitmq应用
[root@node2:~]# rabbitmqctl start_app
Starting node rabbit@node2

```

注意：到此 node2 节点上 也没有 admin 账户了。



## python3测试代码

pika 版本 0.13.0

### 广播模式

#### 生产者

```python
# -*- coding:utf-8 -*-
import pika
import random
import time
from datetime import datetime

VHOST = 'adcvhost'
NODE1_ADDR = '192.168.88.1:5672'
NODE2_ADDR = '192.168.88.2:5672'
NODE3_ADDR = '192.168.88.3:5672'

node1 = pika.URLParameters(f'amqp://admin:admin@{NODE1_ADDR}/{VHOST}')
node2 = pika.URLParameters(f'amqp://admin:admin@{NODE2_ADDR}/{VHOST}')
node3 = pika.URLParameters(f'amqp://admin:admin@{NODE3_ADDR}/{VHOST}')

all_endpoints = [node1, node2, node3]

random.shuffle(all_endpoints)

is_disconnected = True

print("生产者: 首次连接...")
while is_disconnected:
    for url in all_endpoints:
        try:
            connection = pika.BlockingConnection(url)
            is_disconnected = False
        except Exception as e:
            print(f'url连接失败：{url.host}')
            continue

        try:
            print(f'url = {url.host}')
            channel = connection.channel()
            channel.basic_qos(prefetch_count=1)

            channel.exchange_declare(exchange='logs', exchange_type='fanout')

            while True:
                message = f'info: Hello World!-{datetime.now()}'
                channel.basic_publish(exchange='logs', routing_key='', body=message)
                print(f" [x] Sent {message}")
                time.sleep(1.5)
        except Exception as e:
            connection.close()
            is_disconnected = True
            print(f'生产过程中报错：{e}')
            break

    print("生产者: 重新连接...")

```

#### 消费者

```python
# -*- coding:utf-8 -*-
import pika
import random

VHOST = 'adcvhost'
NODE1_ADDR = '192.168.88.1:5672'
NODE2_ADDR = '192.168.88.2:5672'
NODE3_ADDR = '192.168.88.3:5672'

node1 = pika.URLParameters(f'amqp://admin:admin@{NODE1_ADDR}/{VHOST}')
node2 = pika.URLParameters(f'amqp://admin:admin@{NODE2_ADDR}/{VHOST}')
node3 = pika.URLParameters(f'amqp://admin:admin@{NODE3_ADDR}/{VHOST}')

all_endpoints = [node1, node2, node3]

random.shuffle(all_endpoints)


def callback(ch, method, properties, body):
    print(f" [x] {body}")


is_disconnected = True

print("消费者: 首次连接...")
while is_disconnected:
    for url in all_endpoints:
        try:
            connection = pika.BlockingConnection(url)
            is_disconnected = False
        except Exception as e:
            print(f'url连接失败：{url.host}')
            continue

        try:
            print(f'url = {url.host}')
            channel = connection.channel()
            channel.basic_qos(prefetch_count=1)

            channel.exchange_declare(exchange='logs', exchange_type='fanout')

            result = channel.queue_declare(queue='', exclusive=True)
            queue_name = result.method.queue

            channel.queue_bind(exchange='logs', queue=queue_name)

            print(' [*] Waiting for logs. To exit press CTRL+C')

            channel.basic_consume(
                queue=queue_name, consumer_callback=callback, no_ack=True)

            channel.start_consuming()
        except Exception as e:
            connection.close()
            is_disconnected = True
            print(f'消费过程中报错：{e}')
            break

    print("消费者: 重新连接...")

```



# 常用命令

```shell
# 强杀rabbitmq相关进程
# ps -ef | grep rabbitmq | grep -v grep | awk '{print $2}' | xargs kill -9

# 查看集群状态
sudo rabbitmqctl cluster_status

# 查看rabbitmq服务状态
sudo systemctl status rabbitmq-server.service

# 停止rabbitmq服务
sudo systemctl stop rabbitmq-server.service

# 启动rabbitmq服务
sudo systemctl start rabbitmq-server.service

# 注意这里停止的是 rabbitmq应用 不是 rabbitmq 服务（Stopping rabbit application）
sudo rabbitmqctl stop_app

# 添加当前节点到集群 rabbit@node1
sudo rabbitmqctl join_cluster rabbit@node1

# Starting rabbit application
sudo rabbitmqctl start_app

# 查看用户列表
sudo rabbitmqctl list_users

# 查看 vhost 列表
sudo rabbitmqctl list_vhosts

# 重启服务
sudo systemctl restart rabbitmq-server
```



