# Detecting Dead TCP Connections with Heartbeats and TCP Keepalives

[官网地址](https://www.rabbitmq.com/heartbeats.html)

## 总览

网络可能以多种方式失败，有时是非常微妙的(例如高比率丢包)。被中断的TCP连接需要相当长的时间(例如，Linux上的默认配置约为11分钟)才能被操作系统检测到。AMQP 0-9-1提供心跳功能，以确保应用层及时发现中断的连接(以及完全没有响应的对等点)。心跳还防御某些网络设备，它们可能会终止在一段时间内没有活动的“空闲“TCP连接。

TCP keepalives是一种TCP堆栈特性，它具有类似的用途，可能非常有用(可能与心跳结合使用)，但是需要对内核进行调优，才能适用于大多数操作系统和发行版。



## Heartbeat Timeout Value

心跳超时值定义了RabbitMQ和客户端库在多长时间之后应该认为TCP连接不可访问（关闭）。这个值在连接时由客户端和RabbitMQ服务器协商。客户端必须配置为请求心跳（request heartbeats）。

协商过程是这样工作的:服务器将建议其可配置值，客户机将其与配置值进行协调，并将结果值发回。该值以秒为单位，RabbitMQ建议的默认值是60。

RabbitMQ核心团队维护的Java、.NET和Erlang客户端使用以下协商算法:

- 如果其中一个值为0(见下面)，则使用两个值中较大的值
- 否则，使用两个中的较小值

零值表示对等点建议完全禁用心跳。要禁用心跳，两个对等点必须选择并使用值0。除非知道环境在每个主机上都使用TCP keepalives，否则强烈建议不要这样做。

非常低的心跳值也强烈建议反对。



## Heartbeat Frames



