# 一、官网demo学习笔记

## 1.1、Hello World

### 1.1.1、常用命令

[官方文档]: https://www.rabbitmq.com/tutorials/tutorial-one-python.html

- 启动rbmq

```shell
rabbitmq-server
```

- 查看rbmq服务器中已有的队列

```shell
sudo rabbitmqctl list_queues
```

- 查看rbmq服务器各个队列中待处理的消息(messages_ready)、未返回ack的消息(messages_unacknowledged)

```shell
sudo rabbitmqctl list_queues name messages_ready messages_unacknowledged
```

- 清楚全部的queue

```shell
清除的命令是： rabbitmqctl reset

但是在使用此命令前，要先关闭应用，否则不能清除。

关闭应用的命令为： rabbitmqctl stop_app

执行了这两条命令后再次启动此应用。

命令为： rabbitmqctl start_app

再次执行命令： rabbitmqctl list_queues

这次可以看到 listing 及 queues都是空的
```



### 1.1.2、最简单的rabbitmq模型

- 原理图

  ![python-one-overall](./images/python-one-overall.png)

  - Producer：消息的生产者
  - Consumer：消息的消费者
  - Queue：消息队列

### 1.1.3、完整代码

#### 1.1.3.1、producer

```python
#!/usr/bin/env python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()


channel.queue_declare(queue='hello')

channel.basic_publish(exchange='',
                      routing_key='hello',
                      body='Hello World!')
print(" [x] Sent 'Hello World!'")
connection.close()
```

#### 1.1.3.2、consumer

```python
#!/usr/bin/env python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()


channel.queue_declare(queue='hello')

def callback(ch, method, properties, body):
    print(" [x] Received %r" % body)

channel.basic_consume(callback,
                      queue='hello',
                      no_ack=True)

print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()
```





## 1.2、多个consumer监听同一个queue

[官方文档]: https://www.rabbitmq.com/tutorials/tutorial-two-python.html	"多个consumer监听同一个queue"

### 1.2.1、多个consumer监听同一个queue

- 原理图

  ![prefetch-count](./images/prefetch-count.png)

  - Pass

### 1.2.2、声明消息队列的语句是`幂等的`

```python
channel.queue_declare(queue='task_queue', durable=True)
```

### 1.2.3、消息队列持久化

```python
# durable=True 使消息队列持久化
#即使rabbitmq的服务器挂了，再次启动服务器，队列还在
channel.queue_declare(queue='task_queue', durable=True)
```

### 1.2.4、同名的消息队列声明语句必须完全一致(即参数相同)

- 如果声明同一个队列的语句中的参数不同，程序会报错

```python
# 第一次声明使用的语句
channel.queue_declare(queue='hello')
# 第二次声明使用的语句
channel.queue_declare(queue='hello',durable=True)

# 由于第二次的声明语句中的参数 durable=True 与第一次的声明语句不同，所以系统报错，如下
pika.exceptions.ChannelClosed: (406, "PRECONDITION_FAILED - inequivalent arg 'durable' for queue 'hello' in vhost '/': received 'true' but current is 'false'")
```

### 1.2.5、消息持久化

```python
# delivery_mode=2 使消息持久化
channel.basic_publish(exchange='',
                      routing_key='task_queue',
                      body=message,
                      properties=pika.BasicProperties(
                          delivery_mode=2,  # make message persistent
                      ))
# Although it tells RabbitMQ to save the message to disk, there is still a short time window when RabbitMQ has accepted a message and hasn't saved it yet. 
# If you need a stronger guarantee then you can use <publisher confirms>.

# 尽管设置的消息持久化，但是还是有一定的概率会丢失消息
# 例如：当RabbitMQ接收到一个消息，但是还没有来得及存储到硬盘上，RabbitMQ服务器就挂了。这种情况下，该消息就丢失了。
```

### 1.2.6、平均分配message给worker

```python
# 使worker在同一时间只接受一个message(task)
# 避免一个worker处理了很多任务，而其他的worker很闲
channel.basic_qos(prefetch_count=1)
```

### 1.2.7、完整代码

#### 1.2.7.1、producer

```python
#!/usr/bin/env python
import pika
import sys

connection = pika.BlockingConnection(pika.ConnectionParameters(
    host='localhost'))
channel = connection.channel()

channel.queue_declare(queue='task_queue', durable=True)

message = ' '.join(sys.argv[1:]) or "Hello World!"
channel.basic_publish(exchange='',
                      routing_key='task_queue',
                      body=message,
                      properties=pika.BasicProperties(
                          delivery_mode=2,  # make message persistent
                      ))
print(" [x] Sent %r" % message)
connection.close()

```

#### 1.2.7.2、consumer

```python
#!/usr/bin/env python
import pika
import time

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

channel.queue_declare(queue='task_queue', durable=True)
print(' [*] Waiting for messages. To exit press CTRL+C')

def callback(ch, method, properties, body):
    print(" [x] Received %r" % body)
    time.sleep(body.count(b'.'))
    print(" [x] Done")
    ch.basic_ack(delivery_tag = method.delivery_tag)# acknowledgments

# This tells RabbitMQ not to give more than one message to a worker at a time. 
channel.basic_qos(prefetch_count=1) # Worker only receive one message(task) at the same time

channel.basic_consume(callback,
                      queue='task_queue')

channel.start_consuming()
```





## 1.3、exchange广播消息到多个queue

[官方文档]: https://www.rabbitmq.com/tutorials/tutorial-three-python.html	"exchange广播消息到多个queue"

- exchange广播消息到多个queue，原理图

  ![python-three-overall](./images/python-three-overall.png)

### 1.3.1、RabbitMQ的核心思想

- RabbitMQ的核心思想是producer从不直接发送消息给queue。事实上，在大多数情况下，producer甚至根本不知道一条消息是否将会被发送到任何queue
- producer只会把消息发送给一个*exchange*
- exchange在一边接受producer发来的消息，在另一边将消息发送给queue。exchange必须知道如何处理收到的消息。比如，是否应该将消息添加到特定的queue，是否应该将消息添加到多个queue，或者是否应该丢弃这个消息等。处理消息的方式通过 *exchange type* 来定义

### 1.3.2、将exchange收到的信息广播到exchange知道的全部queue中

```python
# exchange_type='fanout'
# 'fanout'类型的exchange可以将从producer收到的信息广播到exchange知道的全部queue中
channel.exchange_declare(exchange='logs',
                         exchange_type='fanout')
```

- 查看rabbitmq服务器中全部的exchanges

```shell
sudo rabbitmqctl list_exchanges
```

### 1.3.3、生成随机queue

```python
# 不指定 queue 参数
# 参数 exclusive=True ，当consumer被关闭时，删除该队列
result = channel.queue_declare(exclusive=True)
# 代码执行后，通过result.method.queue 获取随机名字如"amq.gen-JzTY20BRgKO-HjmUJj0wLg"的queue
```

### 1.3.4、绑定exchange和queue

```python
# 绑定是将一个exchange和一个queue建立通信关系
# queue=result.method.queue 绑定exchange和随机产生的queue
result = channel.queue_declare(exclusive=True)
hannel.queue_bind(exchange='logs',
                   queue=result.method.queue)
```

- 查看rabbitmq服务器中绑定信息

```shell
rabbitmqctl list_bindings
```

### 1.3.5、发消息的时候需要提供routing_key

- The most important change is that we now want to publish messages to our `logs` exchange instead of the nameless one. We need to supply a `routing_key` when sending, but its value is ignored for `fanout exchanges`.

```python
channel.exchange_declare(exchange='logs',exchange_type='fanout')

# producer(message)的routing_key参数的值，会被"fanout"类型的 exchange 忽略
channel.basic_publish(exchange='logs',
                      routing_key='',
                      body=message)
```

### 1.3.6、完整代码

#### 1.3.6.1、producer

```python
#!/usr/bin/env python
import pika
import sys

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

channel.exchange_declare(exchange='logs',
                         exchange_type='fanout')

message = ' '.join(sys.argv[1:]) or "info: Hello World!"
channel.basic_publish(exchange='logs',
                      routing_key='',
                      body=message)
print(" [x] Sent %r" % message)
connection.close()
```

#### 1.3.6.2、consumer

```python
#!/usr/bin/env python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

channel.exchange_declare(exchange='logs',
                         exchange_type='fanout')

result = channel.queue_declare(exclusive=True)
queue_name = result.method.queue

channel.queue_bind(exchange='logs',
                   queue=queue_name)

print(' [*] Waiting for logs. To exit press CTRL+C')

def callback(ch, method, properties, body):
    print(" [x] %r" % body)

channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=True)

channel.start_consuming()
```





## 1.4、Routing

[官方文档]: https://www.rabbitmq.com/tutorials/tutorial-four-python.html	"Routing"

### 1.4.1、routing_key

- 绑定时，routing_key的意义与要绑定的exchange的exchange type相关

- 下面将queue_bind中的routing_key称作binding key，以此来与basic_publish中的routing_key进行区分

- exchange type的种类有：`direct`, `topic`, `headers` and `fanout`

```python
channel.queue_bind(exchange=exchange_name,
                   queue=queue_name,
                   routing_key='black')
```

### 1.4.2、direct exchange

#### 1.4.2.1、direct exchange

- A message goes to the queues whose `binding key` exactly matches the `routing key` of the message.（一个消息，会被添加到binding key与该消息的routing key相同的全部queue中）

- direct exchange 的原理图

  ![direct-exchange](./images/direct-exchange.png)
  - exchange X 绑定了两个queue，Q1、Q2
  - Q1的binding key是orange
  - Q2的binding key有两个，black、green
  - 消息发布者P发出的带有routing key=orange的消息，会添加到Q1中
  - 消息发布者P发出的带有routing key=black或routing key=green的消息，会添加到Q2中
  - 其他的消息将被丢弃

#### 1.4.2.2、direct-exchange-multiple

- direct-exchange-multiple 的原理图

  ![direct-exchange-multiple](./images/direct-exchange-multiple.png)
  - exchange X 绑定了两个queue，Q1、Q2
  - Q1、和Q2的binding key都是black
  - 消息发布者P发出的带有routing key=black的消息，会添同时加到Q1和Q2中
  - 这样就实现了"fanout"类型exchange的广播功能

### 1.4.3、完整代码

#### 1.4.3.1、producer

```python
#!/usr/bin/env python
import pika
import sys

connection = pika.BlockingConnection(
    pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

channel.exchange_declare(exchange='direct_logs', exchange_type='direct')

severity = sys.argv[1] if len(sys.argv) > 1 else 'info'
message = ' '.join(sys.argv[2:]) or 'Hello World!'
channel.basic_publish(
    exchange='direct_logs', routing_key=severity, body=message)
print(" [x] Sent %r:%r" % (severity, message))
connection.close()
```

#### 1.4.3.2、consumer

```python
#!/usr/bin/env python
import pika
import sys

connection = pika.BlockingConnection(
    pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

channel.exchange_declare(exchange='direct_logs', exchange_type='direct')

result = channel.queue_declare(queue='', exclusive=True)
queue_name = result.method.queue

severities = sys.argv[1:]
if not severities:
    sys.stderr.write("Usage: %s [info] [warning] [error]\n" % sys.argv[0])
    sys.exit(1)

for severity in severities:
    channel.queue_bind(
        exchange='direct_logs', queue=queue_name, routing_key=severity)

print(' [*] Waiting for logs. To exit press CTRL+C')


def callback(ch, method, properties, body):
    print(" [x] %r:%r" % (method.routing_key, body))


channel.basic_consume(
    queue=queue_name, on_message_callback=callback, auto_ack=True)

channel.start_consuming()
```





## 1.5、Topic

[官方文档]: https://www.rabbitmq.com/tutorials/tutorial-five-python.html	"Topic"

### 1.5.1、Topic exchange

- topic exchange 的原理图

  ![Topic exchange](./images/Topic exchange.png)
  - `routing_key`的格式为：关键字1.关键字2
    - 例如：`"quick.orange.rabbit"`
  - `*` ：匹配一个关键字
  - `#` ：匹配任意多个关键字
  - 在上图中：
    - 带有routing_key = `"quick.orange.rabbit"` 的消息会被添加到Q1、Q2中
    - 带有routing_key = `"lazy.orange.elephant"` 的消息也会被添加到Q1、Q2中
    - 带有routing_key = `"quick.orange.fox"` 的消息会被添加到Q1中
    - 带有routing_key = `"lazy.brown.fox"` 的消息会被添加到Q2中
    - 带有routing_key = `"lazy.pink.rabbit"` 的消息会被添加到Q2中(只会添加一次，尽管两个routing_key都匹配了)
    - 带有routing_key = `"quick.brown.fox"` 的消息没有匹配到任何routing_key，所以该消息将会被丢弃
    - 带有routing_key = `"orange"` 或 `"quick.orange.male.rabbit"` 的消息没有匹配到任何routing_key，所以该消息将会被丢弃
    - 带有routing_key = `"lazy.orange.male.rabbit"` 的消息会被添加到Q2中

- 注意：
  - 当一个queue绑定了 `"#"` ，则这个queue将接受全部消息，功能类似 `fanout exchange`
  - 当一个queue绑定的routing_key中既没用  `"*"` 也没用 `"#"`  ，则功能类似 `direct exchange`

### 1.5.2、完整代码

#### 1.5.2.1、producer

```python
#!/usr/bin/env python
import pika
import sys

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

channel.exchange_declare(exchange='topic_logs',
                         exchange_type='topic')

routing_key = sys.argv[1] if len(sys.argv) > 2 else 'anonymous.info'
message = ' '.join(sys.argv[2:]) or 'Hello World!'
channel.basic_publish(exchange='topic_logs',
                      routing_key=routing_key,
                      body=message)
print(" [x] Sent %r:%r" % (routing_key, message))
connection.close()
```

#### 1.5.2.2、consumer

```python
#!/usr/bin/env python
import pika
import sys

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

channel.exchange_declare(exchange='topic_logs',
                         exchange_type='topic')

result = channel.queue_declare(exclusive=True)
queue_name = result.method.queue

binding_keys = sys.argv[1:]
if not binding_keys:
    sys.stderr.write("Usage: %s [binding_key]...\n" % sys.argv[0])
    sys.exit(1)

for binding_key in binding_keys:
    channel.queue_bind(exchange='topic_logs',
                       queue=queue_name,
                       routing_key=binding_key)

print(' [*] Waiting for logs. To exit press CTRL+C')

def callback(ch, method, properties, body):
    print(" [x] %r:%r" % (method.routing_key, body))

channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=True)

channel.start_consuming()
```





## 1.6、Remote procedure call (RPC)

### 1.6.1、







# 二、pass