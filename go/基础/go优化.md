CPU性能分析

Mem性能分析

使用火焰图进行优化

#### 优化的原理

- 尽可能减少http请求数
- 使用CDN
- gzip压缩
- 优化后端API

#### go程序的优化

CPU维度的优化

Mem维度的优化

锁竞争的优化

#### 开启性能优化

pporf开启后，go的运行时每隔一段时间后(10ms)收集当前堆栈信息，获取各个函数占用的CPU以及内存资源，当pprof完成后，通过对这些采样数据进行分析，形成一个性能分析报告。

- import ("runtime/pprof")
- 开启CPU性能分析,pprof.StartCPUProfile(w io.Write)
  - w 可以是一个网络文件，也可以是本地文件。
- 停止CPU性能分析pprof.StopCPUProfile()

![image-20200413092548880](images\go优化\image-20200413092548880.png)

go tool pprof  .\编译后生成的.exe .\cpu.pprof  会进入交互模式

​	top5 

​		输出cpu使用排行前5的函数，

​			flat 函数使用CPU的耗时

​			flat% 耗时百分比

​			sum% 前面n个函数的百分比之和

​	list 函数名称

​		可以查看 具体代码的耗时 查看的系统函数，我们需要关注的是我们程序种调用这个系统函数的点

​	web 

​		可以在web页面种查看具体的分析，

​		需要安装graphviz ,而后放到go的环境变量路径种

​		![image-20200413094601124](images\go优化\image-20200413094601124-1608819846929.png)