## kafka的配置

https://www.orchome.com/669

## kafka的操作

#### 创建topic

```shel
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic Hello-Kafka
```

#### 查看 topic 

```shell
bin/kafka-topics.sh --list --zookeeper zookeeper1:2181
```

#### 测试消费消息

```shel
kafka-console-producer.sh --broker-list centos2:9093 --topic kafkademo
kafka-console-consumer.sh --zookeeper centos2:2181 --topic kafkademo --from-beginning
```

## kafka和zk

消费这读取kafka中的消息的时候，会去zk中获取kafka的注册地址，而后根据注册地址去消费kafka的消息，

每个broker都需要在zk上注册,

有关所有主题的配置，包括现有主题列表，每个主题的分区数，所有副本的位置，所有主题的配置替代列表以及哪个节点是首选领导者等。

topic由多个分区。分区存在多个broker上，这些对于关系也在zk上维护。

消费者和消费组的信息，当前消费组的消费的offset的信息

## Kafka的基本概念 

> https://kafka.apache.org/documentation/ 官网文档
> https://blog.csdn.net/u013573133/article/details/48142677 个人，比较好 

kafka是分布式流平台，流也就是数据流，

有消费者应用程序。他们从Kafka集群读取消息，对其进行处理，而后再发送到Hadoop，Cassandra，HBase，或者可能再次将其推回Kafka，以供其他人读取这些修改或转换的记录

可以做 topic的访问限制

可以做跨集群的数据复制

#### Broker

在Kafka集群中会有一个或多个broker，其中有一个broker会被选举为控制器（Kafka Controller），它负责管理整个集群中所有分区和副本的状态等工作。比如当某个分区的leader副本出现故障时，由控制器负责为该分区选举新的leader副本。再比如当检测到某个分区的ISR(维护了与leader信息一致的follower的信息)集合发生变化时，由控制器负责通知所有broker更新其元数据信息

#### 偏移量

它是分区中消息的序列号。当消息到达分区时，将分配此编号。这些编号一经分配，便永不改变。他们是一成不变的。这种排序方式意味着Kafka按到达顺序将消息存储在分区中。第一条消息的偏移量为零。下一条消息收到一个偏移量，依此类推。但是请记住，分区之间没有全局偏移量。偏移是分区的局部偏移。因此，如果要查找消息，则应了解三件事。
主题名称，分区号和偏移号

#### Topic

生产者消费数据和消费数据是按照topic来说的。

#### partition

topic下的消息分区，通过key取哈希后把消息映射分发到一个指定的分区，每个分区都映射到broker上的一个目录。一般每个分区存储在一个broker上，消费者的个数决定分区数，分区数不是越多越好，会消耗文件句柄数和服务器资源。同一个消费者可以开启多个线程进行消费，

##### replica

replica：副本， 每个分区按照生产者的消息达到顺序存放。每个分区副本都有一个leader，副本是针对与分区来说的。

leader replica：leader角色的分区副本，leader角色的分区处理消息的读写请求. Leader和follower位于不同的broker.

消费者消费可以并行消费，多个消费者消费

分区数可以大于节点数，但是副本数不能大于节点数量,创建主题是分区数量最好为代理数量的整数倍

##### 消息磁盘存储方式

> https://matt33.com/2016/03/08/kafka-store/

每个分区在底层文件系统都有属于自己的一个目录，命名规则是分区的topic名称+分区号，

这个目录里面是segment file ,这个文件包含两部分。 **.index**索引文件、**.log**消息内容文件。

- log文件

  第一个segment从0开始，后续每个segment文件名为上一个segment文件最后一条消息的offset,ofsset的数值最大为64位（long类型），20位数字字符长度，没有数字用0填充

- index文件

  存储的是key-value格式的，key代表在.log中按顺序开始第n条消息，value代表该消息的物理位置偏移。但是在.index中不是对每条消息都做记录，它是每隔一些消息记录一次，避免占用太多内存。

##### 消息过期删除的机制：

​	Kakfa提供了两种策略来删除数据。一是基于时间，二是基于partition文件大小

##### 分区数多少问题

> https://www.jianshu.com/p/dbbca800f607

#### 生产者

每个消息都是由key和value组成。

键的作用:可以作为消息附加信息，可以决定消息被写道哪个主机的哪个分区，相同的键的消息被写入到同一个分区。

如果key为空

​	则是由kafka分区器挑选一个分区，记录会被随机的发送到topic的分区上。

key不为空

​	通过hash计算出这个key的消息应该在哪个分区

Producers直接发送消息到broker上的leader partition，不需要经过任何中介或其他路由转发。为了实现这个特性，kafka集群中的每个broker都可以响应producer的请求，并返回topic的一些元信息，这些元信息包括哪些机器是存活的，topic的leader partition都在哪，现阶段哪些leader partition是可以直接被访问的。

##### 消息发送方式

同步发送

在producer端设置acks=all：这个是要求每条数据，必须是写入所有replica之后，才能认为是写成功了

在producer端设置retries=MAX（很大很大很大的一个值，无限次重试的意思）：这个是要求一旦写入失败，就无限重试，卡在这里了

异步发送

一旦有了分区号，分区器便可以将其发送给Broker。但是，分区程序不是立即发送消息，而是将消息保留在分区缓冲区中。生产者为每个分区维护一个内存缓冲区，并分批发送记录。(这也是kafka比rocketMQ高效的原因)

#### 消费者

一个partition中的数据可以被不同的消费者组中的一个消费者消费。所以一般消费者组中消费者数量相同和分区数相同。

如果消费者多与partition的数据量。多余的消费者空闲。

消费者知道了消息在哪个broker时候，会直接和broker进行通信消费消息，	 

消费者和消费组的信息，当前消费组的消费的offset的信息

##### 消费过程

> https://blog.csdn.net/laojiaqi/article/details/79034798
> https://www.learningjournal.guru/courses/kafka/kafka-foundation-training/offset-management/

分区中的消息是由偏移量，当消费者获取到消息后偏移后向后移动，消费者处理完消息，可以提交偏移量，

提交的方式

- 自动提交 ，

  可以设置自动提交的间隔，消费者从分区中获取消息后的n秒自动提交，

  如果时间过长会出现消息被重复的消费的问题

- 手动提交

  - 同步提交 提交失败后重试
  - 异步提交 提交失败不会重试

##### 消费者组

一个分区的消息只能被一个同一个消费组中消费者消费一次。

一个消费组中的消费者可以消费多个分区的数据，

##### 消费者消费分区的分区策略，

> https://www.cnblogs.com/hzmark/p/sticky_assignor.html

也就是如果有多个消费者，如何确定消费者 消费哪个分区的数据,

Kafka提供的两种分配策略： range和roundrobin，由参数partition.assignment.strategy指定，默认是range策略。

当以下事件发生时，Kafka 将会进行对消费者所对应消费分区进行重新分配

- 同一个 Consumer Group 内新增消费者
- 消费者离开当前所属的Consumer Group，包括shuts down 或 crashes
- 订阅的主题新增分区

##### 消费者所消费分区重写分配问题

我们使用轮询方法获得了很多数据，并且将花费一些时间来完成对所有记录的处理。如果花费大量时间来处理记录，则将如下问题

长时间不进行轮询，则组协调员可能会认为您已经死了并且触发了重新平衡活动

https://www.learningjournal.guru/courses/kafka/kafka-foundation-training/rebalance-listener/

##### 消费组组协调协议

> zyyok.net/zy/lqmdj.asp

每次重新平衡后，当前这一代的所有成员都开始向组协调器发送定期心跳。只要协调员继续接收心跳，就假定成员健康。在每个收到的心跳上，协调器都会启动（或重置）计时器。如果在计时器到期时未收到任何心跳信号，则协调器会将成员标记为死亡，并向组中的其余成员发出信号，告知他们应重新加入，以便可以重新分配分区。计时器的持续时间称为*会话超时，*并在客户端上通过设置session.timeout.ms进行配置。 


