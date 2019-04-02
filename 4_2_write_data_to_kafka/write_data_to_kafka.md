# 1、 生产者创建消息。
## 1.1 kafka 基本概念
kafka是一个分布式实时数据流平台，可以独立部署在单台服务器上，也可以部署在多台服务器上构成集群，它提供了发布和订阅功能，用户可以发送数据到kafka集群中，也可以从kafka集群中读取数据。
## 1.2 kafka 核心概念 
### 1.2.1 代理(broker)
在kafka集群中，一个kafka进程(kafka实例)被称为一个代理broker节点，代理节点是消息队列中的一个常用概念，通常，部署分布式kafka集群时，一台服务器上部署一个kafka实例。
### 1.2.2 生产者(producer)
在kafka系统中，生产者通常被称为producer，producer将消息记录发送到kafka集群指定的主题中进行存储，同时生产者producer也能通过自定义算法决定将消息记录发送到哪个分区(partiton)
### 1.2.3 消费者(consumer)
消费者从kafka集群指定的主题中读取消息记录。
在读取主题数据时，需要设置消费组名GroupID，如果不设置，则kafka消费者会默认生成一个消费组名称。
### 1.2.4 消费者组(consumer group)
消费者程序在读取kafka系统主题topic的数据时，通常会使用多个线程来执行。一个消费者组可以包含一个或多个消费者程序。使用多分区和多线程模式可以极大提高读取数据的效率。
### 1.2.5 主题(topic)
kafka系统通过主题来区分不同的业务，比如用户登录数据存储在A中，而充值业务存储在B中，那么应用程序订阅了主题A，没有订阅主题B，则应用程序只能读取主题A的数据。
### 1.2.6 分区(partition)
每一个主题中可以有一个或者多个分区(partition)，在kafka系统设计思想中，分区是属于物理层面的，不同的分区对应不同的数据文件。kafka通过分区来支持物理层面的并发读写，以提高kafka集群的吞吐量。每个主题(topic)下的各分区内部消息的记录是有序的，每个消息都有一个连续的偏移量序号。

一个分区只对应一个代理节点(broker)。一个代理节点可以管理多个分区。
### 1.2.7 副本(replication)
在kafka系统中，每个主题topic主题创建的时候会要求指定副本数，默认是1，通过副本机制来保证kafka分布式集群数据的高可用性。
>若集群数量大于3的时候，主题副本可以设置为3，
>若集群的数量小于3，主题副本可以设置为小于等于集群数量值
>
要往kafka写入消息，首先需要创建一个生产者对象，并设置一些属性。kafka有三个必选的属性：

bootstrap.servers: 该属性指定broker的地址清单，地址的格式为host：port，清单不需要包含所后的broker地址，生产者会从给定的broker里面查找到其他broker信息。不过建议至少提供两个broker的信息，一旦其中一个宕机，那么生产者仍然可以连接到集群上。

key.serializer: broker希望接收到的信息的键和值都是字节数组。生产者接口允许使用参数化类型，因此可以把java对象作为键和值发送给broker。这样的代码具有良好的可读性，不过生产者需要知道如何把这些java对象转换成字节数组。key.serializer必须被设置为一个实现了org.apache.kafka.common.serialization.Serializer接口的类，生产者会使用这个类把键对象序列化成字节数组，kafka客户端默认提供ByteArraySerializer、StringSerializer和IntegerSerializer，因此，如果你只是使用常见的几种java对象类型，那么就没有必要实现自己的序列化器。*key.serializer是必须设置的*，就算是你打算只发送值内容。

value.serializer与key.serializer一样，value.serializer指定的类会将值序列化，如果键和值都是字符串，可以使用key.serializer一样的序列化器，如果键是整数类型而值是字符串，那么需要不同的序列化器。