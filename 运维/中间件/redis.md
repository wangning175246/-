#### k8s自动加入集群

IPLIST=`nslookup es-data.default.svc.cluster.local | grep 'Address: '| awk -F ':' '{printf " %s:3306",$2}'`

#### 3.0

redis-trib.rb create --replicas 1 ip:端口

replicas 表示一个主节点至少需要一个从节点，

redis-trib.rb create  --replicas 1 `nslookup redis.default.svc.cluster.local | grep 'Address: '| awk -F ':' '{printf " %s:6379",$2}'`

#### 5.0 

redis-cli --cluster create  ip:端口 --cluster-replicas 1

#### 删除是失效节点 

cluster forget node-id 删除失效的节点。

#### redis 监听的端口

6379 对外提供服务的端口 

16379 集群内部通讯的端口 Gossip redis节点之间内部通信的协议。

#### redis的内存设定

maxmemory 0 表示不限制，也就是不做缓存淘汰

可以根据自己需求设置，快达到maxmemory的时候会进行缓存清理

#### redis登陆

redis-cli -h 127.0.0.1 -a ZSyule201810 -n 21

#### redis-stat监控 

yum install ruby  

yum install rubygems

gem install redis-stat

redis-stat 172.31.215.24:6379 172.31.215.23:6379 172.31.215.26:6379 172.31.215.22:6379 172.31.215.27:6379 172.31.215.21:6379 10 -a ZSyule201810

#### 统计rdb文件中的所有key的大小

yum -y install python-pip python-redis

pip install rdbtools

rdb -c memory /data/redis/datadir/dump.rdb > result.csv

### redis的备份方式

> http://oldblog.antirez.com/post/redis-persistence-demystified.html
> https://redis.io/topics/persistence

如果先开启的rbd,已经运行一段实际后还需要开启RDB，则需要先在命令行开启AOF，而后修改配置文件。

redis启动的时候，如果开启了AOF，则之后从AOF恢复，不会从RDB进行恢复。

#### RDB

rdb 备份方式，可以执行 save(阻塞主进程，客户端无法连接redis) 或者bgsave（不会阻塞进程）

把内存中的数据，保存到rdb文件中(默认开启的)

Redis在执行主->从同步时也使用RDB快照

##### 配置

```
save 60 10000
60s 有10000个key发生变化就同步
AOF
```

#### AOF

把所有的所有的操作数据的语句保存到aof文件中，

AOF 同步缓存区的命令到磁盘的有三种方式，也就是appendfsync有三个值,

> https://zhuanlan.zhihu.com/p/75894903

把数据写入文件一般是会先陷入内核，缓冲区，而后内核缓冲区会在满足条件的情况下(一般是30s)，把内核缓冲区的内容，下如文件。或者我们执行sync把内核缓冲区的文件同步到磁盘

always 每次事件完成后都需要把缓冲区的内容写入文件，而后执行sync，

everysec 每次事件完成后都需要把缓冲区的内容写入文件，每隔1s 执行一次sync

no 每次事件完成后都需要把缓冲区的内容写入文件，从不执行sync，等待内核决定什么时候执行。

先保存到系统缓存区，而后每隔1s刷一次缓冲区到磁盘的aof文件

##### 配置

```
appendonly yes 开启AOF
appendfilename "appendonly.aof"
appendfsync everysec 保存缓存区中的内容到磁盘的方式，并且每秒执行一次
auto-aof-rewrite-percentage 100 rewrite发生的条件,aof文件是上次的1倍的时候进行重写
auto-aof-rewrite-min-size 64mb rewite发生的条件，AOF大于64M，以上两个条件都满足才会发生重写
```

#### 同时配置

如果先开启的RDB,已经运行一段实际后还需要开启AOF，则需要先在命令行开启AOF，而后修改配置文件。

redis启动的时候，如果开启了AOF，则之后从AOF恢复，不会从RDB进行恢复。

#### 主从同步

从服务器第一次启动是进行全量同步，全量同步完后，后续的同步时增量同步，也就是主接收到一条写命令，会把这个命令发送到从服务器。

每个Redis主数据库都有一个复制ID和偏移量：Replication ID, offset

当副本连接到主数据库时，它们使用[PSYNC](https://redis.io/commands/psync)命令来发送其旧的主数据库复制ID和到目前为止已处理的偏移量。这样主机可以只发送所需的增量部分。但是，如果主缓冲区中的*积压*不足，或者副本引用的历史记录（副本ID）不再已知，则会发生完全重新同步：在这种情况下，副本将获得数据集的完整副本， 从头开始。

> https://www.cnblogs.com/kismetv/p/9236731.html

##### 配置

```
salveof 127.0.0.1 9379 # 只要配置这个，表示这是个从库。
```

##### 手动主从切换

连接slave 执行slaveof NO ONE

#### redis的架构方案

* redis持久化

  AOF  

  RDB

* 备份方案

  本地备份

* 容灾方案

  异地备份

* replication副本级制

  集群化多个节点共同存储所有数据，每个节点有从节点

* sentinal哨兵

  自动的主从切换

#### Redis 数据分片

为了使得集群能够水平扩展，首要解决的问题就是如何将整个数据集按照一定的规则分配到多个节点上，常用的数据分片的方法有：范围分片，哈希分片，一致性哈希算法，哈希槽等 

redis采用的虚拟hash槽分配。Redis Cluster 采用虚拟哈希槽分区，所有的键根据哈希函数映射到 0 ~ 16383整数槽内，计算公式：slot = CRC16(key) & 16383。每一个节点负责维护一部分槽以及槽所映射的键值数据。

为什么是16383 (https://www.cnblogs.com/rjzheng/p/11430592.html) 

![示意图](https://user-gold-cdn.xitu.io/2019/5/24/16aea012c0a66c54?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

#### 分片元数据的存储

> https://stor.51cto.com/art/202004/613829.htm

Cluster中的每个节点都维护一份在自己看来当前整个集群的状态

1. *当前集群状态*
2. *集群中各节点所负责的slots信息，及其migrate状态*
3. *集群中各节点的master-slave状态*
4. *集群中各节点的存活状态及不可达投票*

节点之间通过16379协议进行通信，简单之间通过Gossip协议不断的信息， Gossip 协议会将每个节点管理的槽信息发送给其他节点，收到发送节点的节点槽信息以后，接受节点会将这些信息保存到本地的 clusterState 的结构中，其中 Slots 的数组就是存放每个槽对应哪些节点信息。

Redis Cluster 的通讯机制实际上保证了每个节点都有其他节点和槽数据的对应关系。

Redis 的客户端无论访问集群中的哪个节点都可以路由到对应的节点上，因为每个节点都有一份 ClusterState，它记录了所有槽和节点的对应关系。

![img](https://s3.51cto.com/oss/202004/05/8b916ee83adfac9d8550090caa88d4dc.jpg-wh_600x-s_591866780.jpg)

Redis 客户端通过 CRC16(key)%16383 计算出 Slot 的值，发现需要找“缓存节点 1”读/写数据，但是由于缓存数据迁移或者其他原因导致这个对应的 Slot 的数据被迁移到了“缓存节点 2”上面。那么这个时候 Redis 客户端就无法从“缓存节点 1”中获取数据了。但是由于“缓存节点 1”中保存了所有集群中缓存节点的信息，因此它知道这个 Slot 的数据在“缓存节点 2”中保存，因此向 Redis 客户端发送了一个 MOVED 的重定向请求。这个请求告诉其应该访问的“缓存节点 2”的地址。Redis 客户端拿到这个地址，继续访问“缓存节点 2”并且拿到数据。

#### Gossip 协议

> https://juejin.im/post/5dd65d676fb9a05a9a22ac6f

Redis 集群是去中心化的，彼此之间状态同步靠 gossip 协议通信，集群的消息有以下几种类型

**Meet** 通过「cluster meet ip port」命令，已有集群的节点会向新的节点发送邀请，加入现有集群。

**Ping** 节点每秒会向集群中其他节点发送 ping 消息，消息中带有自己已知的两个节点的地址、槽、状态信息、最后一次通信时间等。

**Pong** 节点收到 ping 消息后会回复 pong 消息，消息中同样带有自己已知的两个节点信息。

**Fail** 节点 ping 不通某节点后，会向集群所有节点广播该节点挂掉的消息。其他节点收到消息后标记已下线。

例如当加入新节点时(meet)，只有邀请节点和被邀请节点知道这件事，其余节点要等待 ping 消息一层一层扩散。除了 Fail 是立即全网通知的，其他诸如新节点、节点重上线、从节点选举成为主节点、槽变化等，都需要等待被通知到，也就是Gossip协议是最终一致性的协议。

由于 gossip 协议对服务器时间的要求较高，否则时间戳不准确会影响节点判断消息的有效性。另外节点数量增多后的网络开销也会对服务器产生压力，同时结点数太多，意味着达到最终一致性的时间也相对变长，因此官方推荐最大节点数为1000左右

