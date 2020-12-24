## java

tomcat 启动的时候会加载一些系统变量 JAVA_OPTS, CATALINA_OPTS

CATALINA_OPTS="$JPDA_OPTS $CATALINA_OPTS"

JAVA_OPTS="$JAVA_OPTS $JSSE_OPTS"

java 直接启动的jar的包时候是会加载 JAVA_OPTS JAVA_TOOL_OPTIONS 这个两个环境变量的。

## jvm优化

#### STW

Java中Stop-The-World机制简称*STW*,是在执行垃圾收集算法时,Java应用程序的其他所有线程都被挂起

#### JVM 堆内存划分

年轻代

​	Eden 新生区
​	survivor  幸存区 里面的两个区大小相同地位相同，且可进行角色互换的空间块。

​		to

​		from

老年代

永生区

#### 垃圾收集器

> https://juejin.im/post/5b651200f265da0fa00a38d7

##### 收集算法

- 引用计数法

  对象之间相互引用，多个对象的引用技术不为0，可能造成内存泄漏(引用的内存没有释放,对象之间的循环引用)

- 标记清除法

  标记阶段，从根节点开始标记较大的对象。

  清除阶段，清除所有未被标记的对象。

  回收的空间不是连续的。

- 标记-整理法(标记压缩法)

  也就是在标记清除法后会对内存中的内容进行整理。

  在标记清除法的基础上解决内存不连续的问题。

- 复制算法

  复制算法将内存划分为两个区域，所有生成的对象都只能分配在其中的一个区域活动，另一个区域是空闲的。

  复制算法同样从根集合扫描，将存活的对象复制到空闲区域，扫描完成后会将活动区域中的对象一次性全部回收，此时原本空闲的区域变成活动区域，下一次GC时重复此操作。

  适用于存活对象比较少的时候，对象存活率比较低的场景。极为高效。

老年代内存回收一般使用标记整理法，新生代的内存回收一般使用复制算法。

##### 垃圾回收器分类

* 新生代单线程收集器 Serial

* 老年代单线程收集器 Serial Old

* 新生代多线程收集器 ParNew，属于 `Serial` 回收器的多线程版本

* 新生代并行回收 (Parallel Scavenge) 收集器

* 老年代并行回收收集器

* CMS 收集器 

  > https://www.cnblogs.com/woshimrf/p/jvm-garbage.html

  开启CMS

  ```shell
  -XX:+UseParNewGC -XX:+UseConcMarkSweepGC
  ```

  老年代的回收器  回收器是在最短回收停顿时间前提的回收器，属于多线程回收器,采用 标记-清除算法。

* G1 收集器 (Garbage First) 对整个堆内存进行垃圾回收。

  `G1` 是 `JDK 1.7` 中正式投入使用的用于取代 `CMS` 的 压缩回收器,它虽然没有在物理上隔断 新生代与 老生代，但是仍然属于 分代垃圾回收器。

多线程的收集器，一般根据cpu的个数来确定收集器的线程数，一般和cpu属性相等，

##### JDK各个版本默认的收集器

查看默认的收集算法

```shell
java -XX:+PrintCommandLineFlags -version 
-XX:G1ConcRefinementThreads=8 -XX:InitialHeapSize=265989440 -XX:MaxHeapSize=4255831040 -XX:+PrintCommandLineFlags -XX:ReservedCodeCacheSize=251658240 -XX:+SegmentedCodeCache -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC(使用G1) -XX:-UseLargePagesIndividualAllocation
java version "10.0.2" 2018-07-17
```

jdk1.7 默认垃圾收集器Parallel Scavenge（新生代）+Parallel Old（老年代）

jdk1.8 默认垃圾收集器Parallel Scavenge（新生代）+Parallel Old（老年代）

jdk1.9 默认垃圾收集器G1

##### GC 分类

Full GC

​	Major GC(老年代GC) 是永生代的垃圾收集，一般主要发生Major GC 就一定会发生Full GC

- System.gc()方法的调用
- 老年代空间不足
- 永生代空间不足
- CMS GC时出现promotion failed和concurrent mode failure
- 堆中分配很大的对象的时候，但是空间不足的时候。

Minor GC  新生代GC

​	Young GC

​	Old GC

#### 优化方法

- -Xmx 堆内存的最大值
- -Xms 初始化堆内存的值
- -Xmn 设置年轻代的大小，年轻代的值+老年代的值等于最大对内存，整个堆的3/8
- -Xss 设置每个线程的堆栈大小。JDK5.0以后每个线程堆栈大小为1MB，以前每个线程堆栈大小为256K。应根据应用线程所需内存大小进行调整。
- -XX:NewRatio=4：设置年轻代（包括Eden和两个Survivor区）与年老代的比值（除去持久代）。设置为4，则年轻代与年老代所占比值为1：4，年轻代占整个堆栈的1/5
- -XX:SurvivorRatio=8：设置年轻代中Eden区与Survivor区的大小比值。设置为8，则两个Survivor区与一个Eden区的比值为2:8，一个Survivor区占整个年轻代的1/10
- -XX:PermSize=100m：初始化永久代大小为100MB。
- -XX:MaxPermSize=256m：设置持久代大小为256MB。
- -XX:MaxTenuringThreshold=15：设置垃圾最大年龄。如果设置为0的话，则年轻代对象不经过Survivor区，直接进入年老代。对于年老代比较多的应用，可以提高效率。如果将此值设置为一个较大值，则年轻代对象会在Survivor区进行多次复制，这样可以增加对象再年轻代的存活时间，增加在年轻代即被回收的概论。
- -XX:+DisableExplicitGC：禁止运行期显式地调用System.gc()来触发fulll GC。
- -XX:CMSFullGCsBeforeCompaction=4：指定进行多少次fullGC之后，进行tenured区 内存空间压缩。
- -XX:ConcGCThreads=4：CMS垃圾回收器并行线程线，推荐值为CPU核心数。
- -XX:ParallelGCThreads=8：新生代并行收集器的线程数。
- -XX:+UseConcMarkSweepGC 使用它来启用CMS垃圾收集器。
- -XX:CMSInitiatingOccupancyFraction=60：CMS垃圾收集器，当老年代达到60%时，触发CMS垃圾回收。

​	垃圾回收的方式，进行垃圾回收的线程数，停顿时间：

​		新生代和老年代的垃圾回收的方式，

​		https://www.ibm.com/developerworks/cn/java/j-lo-JVMGarbageCollection/

​	堆空间的大小

​	新生代，老年代，持久带空间大小或者比例。

​	减少Full GC的次数，增大Minor Gc的次数

​	优化示例：https://juejin.im/post/5dc8d0ea518825592c566a5d#heading-8

## 辅助工具

#### 命令查看jstat

jstat -gc  6 1000 2 每隔1000ms输出一次，持续2个1000ms

​	https://www.cnblogs.com/yjd_hycf_space/p/7755633.html

https://blog.csdn.net/J080624/article/details/85679252

#### arthas

## 监控

#### jmx的开启

docker run -d  --name tomcat-3 -v /root/jmx-prometheus:/jmx-exporter -e CATALINA_OPTS="-Djava.rmi.server.hostname=47.75.150.158 -Dcom.sun.management.jmxremote.port=8899 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.rmi.port=8899" -p 8899:8899 tomcat:8.5-alpine 

-Djava.rmi.server.hostname 指定的是主机名

 -Dcom.sun.management.jmxremote.port 指定的端口

 -Dcom.sun.management.jmxremote.ssl=false 是否进行ssl认证

 -Dcom.sun.management.jmxremote.authenticate=false 是否进行账户密码认证

 -Dcom.sun.management.jmxremote 是否是远程连接。

 -Dcom.sun.management.jmxremote.rmi.port 如果是在docker中还需要指定这个

而后可以通过下载 https://visualvm.github.io/ 进行测试连接

#### proumetheus监控jvm

docker run -d --name tomcat-2 -v /root/jmx-prometheus:/jmx-exporter -e CATALINA_OPTS="-Xms64m -Xmx128m -javaagent:/jmx-exporter/jmx_prometheus_javaagent-0.3.1.jar=6060:/jmx-exporter/simple-config.yml" -p 6062:6060 -p 8082:8080 tomcat:8.5-alpine

这个目录jmx-prometheus下需要两个文件

jmx_prometheus_javaagent-0.3.1.jar 下载路径 https://github.com/prometheus/jmx_exporter

simple-config.yml 他的其他配置参照 https://github.com/prometheus/jmx_exporter

lowercaseOutputName: true

whitelistObjectNames: ["java.lang:type=OperatingSystem"]

rules:

 \- pattern: 'java.lang<type=OperatingSystem><>((?!process_cpu_time)\w+):'

  name: os_$1

  type: GAUGE

  attrNameSnakeCase: true