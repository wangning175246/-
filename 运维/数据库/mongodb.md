mongodb的基本

​	db 数据库

​	collection db中的集合，

​	是基于journaling log 做宕机恢复，类比于mysql的redo log

​	主从复制时基于oplog复制同步的

#### journaling log

> https://www.cnblogs.com/yugongmengjiutian/articles/5520313.html

journaling(日记) 日志功能则是 MongoDB 里面非常重要的一个功能 ， 它保证了数据库服务器在意外断电 、 自然灾害等情况下数据的完整性。它通过预写式的redo日志为MongoDB增加了额外的可靠性保障。开启该功能时,MongoDB会在进行写入时建立一条Journal日志,其中包含了此次写入操作具体更改的磁盘地址和字节。因此一旦服务器突然停机，可在启动时对日记进行重放，从而重新执行那些停机前没能够刷新到磁盘的写入操作。

#### oplog

oplog是固定大小，他只能保存特定数量的操作日志，通常oplog使用空间的增长速度跟系统处理写请求的速度相当，如果主节点上每分钟处理1KB的写入数据，那么oplog每分钟大约也写入1KB数据。如果单次操作影响到了多个文档（比如删除了多个文档或者更新了多个文档）则oplog可能就会有多条操作日志。db.testcoll.remove() 删除了1000000个文档，那么oplog中就会有1000000条操作日志。如果存在大批量的操作，oplog有可能很快就会被写满了

#### 主从复制相关

主节点会把本服务的与写有关的操作记录下来,读操来不记录,这些操作就记录在local数据库中的oplog.$admin这个集合中,这是一个固定集合,大小是可以配置的,主要是通过配置oplogSize这个参数来实现,单位是M,大小一般为磁盘剩余空间的5%左右

因为是固定集合所以当固定集合放满日志的时候,新进来的日志就会把最旧的日志覆盖掉,如果这个值设置的不合理,导致数据很快的被覆盖,而从节点没有来得及更新,这样就会产生数据不同步的情况.设置为主节点的local数据库有会有oplog.$admin与slave这两个集合.slave记录的是从节点的信息.

从节点与主节点的数据同步主要是从节点定时的会去连接主节点,请求主节点的操作日志,从而对自己的数据副表进行同样的操作来达到数据的同步.从节点的local数据库中会多了source与me这两个集合,source是记录主节点的信息,me是记录从节点的标识

#### 基本的架构

![image-20200516071614210](images\mongodb\image-20200516071614210.png)

mongodb集群

部署mongodb集群需要部署4个服务，这4个服务实际都是mongo，只不过在集群中的角色不同。

* config server 

  配置服务器，存储所有数据库元信息（路由、分片）的配置。mongos本身没有物理存储分片服务器和数据路由信息，只是缓存在内存里，配置服务器则实际存储这些数据。mongos第一次启动或者关掉重启就会从 config server 加载配置信息，以后如果配置服务器信息变化会通知到所有的 mongos 更新自己的状态，这样 mongos 就能继续准确路由。在生产环境通常有多个 config server 配置服务器，因为它存储了分片路由的元数据，防止数据丢失！

  config server 也是一个mongo的shard，有多个副本，只不过这个shard里面不会存放 用户的数据，只是存放一个配置的信息。

* mongos 

  数据库集群请求的入口，所有的请求都通过mongos进行协调，不需要在应用程序添加一个路由选择器，mongos自己就是一个请求分发中心，它负责把对应的数据请求请求转发到对应的shard服务器上。在生产环境通常有多mongos作为请求的入口，防止其中一个挂掉所有的mongodb请求都没有办法操作。

* shard 

  一个shard 就是一组副本，也就是一组mongo，多个mongo进程，

  MongoDB是在collection(集合)级别实现的水平分片

  mongodb中一个集合可以分布在多个shard中的，需要配置才可以实现。

  一个shard可以有多个副本，多个副本会选举出一个主shard对外提供服务写服务

  主和副本shard同步是通过oplog是实现的。可以通过oplog做增量备份。

  shard的一个副本对应一台服务器上一个mongo进程。

  默认主要主副本对外提供读写操作，如果想让从副本也对外提供读写操作需要额外设置

**所有的角色都是启动mongos进程，只不过启动的时候加的参数不一样，**

#### shard

使用 MongoDB sharding 后，数据会以 chunk 为单位（默认64MB）根据 `shardKey` 分散到后端1或多个 shard 上。刚开始会生成一个[minKey, maxKey] 的 chunk，该 chunk 初始会存储在 `primary shard` 上，然后随着数据的写入，不断的发生 chunk 分裂及迁移

进行分shared的时候，需要指定条件，按照条件对数据进行分区，每个区上的数据是有一定的规律的，

如果查询的不是按照share key查询的，则请求会被发给所有shared,如果是按照share key 查询，请求可能只是分配给某个shared

https://mongoing.com/archives/4368

shard中有主副本，从副本，主副本接受读写操作，默认情况下从副本不接受读写操作，可以设置从服务接受读操作。

* 延时副本

  设置延时复制主副本中内容

* 仲裁副本

  不复制主中的数据，只是在其投票功能(选举新主的时候投票)

#### 基本操作命令

* 登陆到某台副本上可以执行的命令

  use admin 切换到admin用户

  db.getMongo().setSlaveOk(); 设置副本节点可以读。

  rs.conf() 查看shard的配置

  db.printReplicationInfo() 查看oplog的情况

   db.getReplicationInfo() 查看oplog的情况

  sh.status() 查看shard的信息

  db.table1.stats() 查看某个集合或者table是否开了分片

  db.table1.getShardDistribution() 查看table1的数据分片

* db相关

  use dbname 如果库不存在就创建

  show dbs 查看库

* 集合

  show collections 查看集合

  db.createCollection("my_collection")

* 插入数据

  db.my_collection.inser tOne({uid:1000,name:"xiaoming"})

* 查询

  db.my_collection.find()

* 设置某个db启用分片，需要在admin db中进行设置

  use admin

  db.runCommand( { enablesharding :"testdb"}); 设置某个库启用分片

  db.runCommand( { shardcollection : "testdb.table1",key : {id: 1} } ) 设置shard 分片条件

* shard的数据分片

  数据分片只是对后面的数据进行分片，不会对已有的数据进行分片，
  
* 聚合查询：

  查询的时候传入的是数组，前一个的过滤结果，作为后一个结果的输入，以此类推

  db.my_collection.aggregate([{$uuwind:"$likes"}])

* 查看帮助

  命令.help()

  如 db.my_collection.help()

#### mongo主和副本节点数据大小不一致的原因

​	主要是因为是顺序读写，所以会产生大量的磁盘碎片。造成主上数据目录比从上的数据目录大。

#### mongo 磁盘空间的释放

频繁删除数据之后，会产生很多磁盘碎片，这些碎片如果没有被重复利用，进而会影响查询性能（表查询时仍然会扫描这部分删除数据的磁盘空间块），随需要处理之
当从MongoDB中删除文档（Documents）或集合（Collections）后，MongoDB不会将Disk空间释放给OS，MongoDB在数据文件（Data Files）中维护Empty Records的列表。当重新插入数据后，MongoDB从Empty Records列表中分配存储空间给新的Document，因此，不需要重新开辟空间。为了更新有效的重用Disk空间，必须重新整理数据碎片。
有好几种方法处理：

- 使用compact命令

  在compact期间会阻塞其他针对此collection的操作，所以最好在业务不繁忙的时候进行compact动作

  compact是mongo中的压缩命令。可以整理删除数据产生的碎片。

  在WiredTiger 数据库引擎下，该命令会将整理出的空间释放给操作系统。

  MMAPv1引擎下，compact会整理碎片，重建索引，但不会将未使用的空间释放给系统，后续新插入的数据依然可以使用这些空间

   db.runCommand({ compact: 'collection name',force: 'boolen' } )  ---红色部分是可选项

  compact命令不会自动复制到secondary节点执行，compact在每个节点成员中都是独立的，在Primary，secondary中执行时都要使用force参数，
            当在secondary的collection上执行compact命令时，此secondary节点会变成RECOVERING状态，且无法提供读操作

- 重建collection

- 新加一个secondary节点，然后将此节点切换为primary（数据量非常大的情况下，为了不影响业务）

#### mongo内存中数据分类

* MongoDB 使用 Google tcmalloc 作为内存分配器，内存占用的大头主要是「存储引擎」与 「客户端连接及请求的处理」

* 索引、namespace，未提交的write，query缓冲等

wiredTiger对内存使用会分为两大部分，一部分是内部内存，另外一部分是文件系统的缓存。内部内存默认值有一个计算公式{ 50% of(RAM-1GB) ,or256MB }，索引和集合的内存都被加载到内部内存，索引是被压缩的放在内部内存，集合则没有压缩。wiredTiger会通过文件系统缓存，自动使用其他所有的空闲内存，放在文件系统缓存里面的数据，与磁盘上的数据格式一致，可以有效减少磁盘I/O

--wiredTigerCacheSizeGB限制WiredTiger内部高速缓存的大小。操作系统将使用可用的空闲内存进行文件系统缓存，从而允许压缩的MongoDB数据文件保留在内存中。此外，操作系统将使用任何可用的RAM来缓冲文件系统块和文件系统缓存。

这个需要调的小点。

查看 db.serverStatus() 可以通过这个查看wiredTigerCacheSizeGB他的限制大小。

`maximum bytes configured" : 7871660032,`

#### 备份方案

* 备份文件系统快照的方式备份

* 停止mongo服务，使用cp复制数据文件的方式进行备份

* mongodump备份 mongorestore工具恢复，

  ###### 备份

  ​	mongodump --host localhost --port 27017 --oplog  --gizp 

  ​	--db test 指定db名称

  ​	--collection 集合名称

  ​	--out 指定存放的路径

  ​	--host 指定主机

  ​	--port 指定 端口

  ​	--oplog 回记录备份的过程中数据更改的日志

  ​	-q 查询条件，可以dump出满足查询条件的数据

  ###### 恢复

  ​	mongorestore --oplogReplay --port 3017 存放的路径

  ###### oplog备份

  ​	mongodump --host 192.168.137.131 --port 27002 -d local -c oplog.rs -o /opt/backup/0706local/ 

  ​	mongodump --host 127.0.0.1:27017 -d local -c oplog.rs -u root -p root --authenticationDatabase admin -q '{ts:{$gt: Timestamp(1512958289,1), $lt: Timestamp(1512958407,1)}}' --out - > dump/oplog.bson

  ###### oplog的恢复

  ​	mongorestore --host 192.168.137.132 --port 27002 --oplogReplay  --dir /opt/backup/0706local/

* 设置一个延迟复制的副本，而后在延迟复制副本上进行全量备份，而后在主的副本上进行oplog的增量备份。

