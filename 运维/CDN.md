 CDN选择的考虑

​	价格

​	所能承受的最大攻击

​	节点资源的共享和独享

CDN相关的博客

| 相关知识           | 地址                                                         |
| ------------------ | :----------------------------------------------------------- |
| GSLB               | http://chongit.github.io/2015/04/15/GSLB%E6%A6%82%E8%A6%81%E5%92%8C%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86/ |
| AnyCast            | https://www.cnblogs.com/zafu/p/9168617.html                  |
| 一名CDN老兵的分享  | https://mp.weixin.qq.com/s?__biz=MjM5MzM3NjM4MA==&mid=215672882&idx=1&sn=072dd242579cad23ee3c9557b54bc78f&scene=1&srcid=0923nJPyzlwlHbyM96KzxU03 |
| 网站优化方式       | https://www.cloudxns.net/Support/detail/id/915.html          |
| CDN图解            | https://www.verycdn.cn/product/mix#product_mix               |
| 阿里云动态加速问题 | https://blog.csdn.net/lianjiangdai/article/details/83307012  |
| CND之我见          | https://yq.aliyun.com/articles/577708                        |

CDN产品

​	mlytics.com

CDN

动态内容加速

当用户对网站的动态内容发起请求时，CDN管理中心的智能负载均衡系统、动态调配系统会根据其携带的本地DNS及各节点状态快速分配距离用户最近且服务性能最优的边缘加速节点A，同时云计算平台根据实时监控数据动态选取与网站源服务器负载、链路状况等性能最优的边缘加速节点B，用户请求的动态内容在边缘加速节点A与边缘加速节点B之间利用私有协议进行可靠的快速数据传输，并由边缘加速节点B与源服务器进行实时交互，保证动态加速的服务效果

![img](.\images\动态加速.png)