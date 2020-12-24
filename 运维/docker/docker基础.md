#### NameSpace

namespace 主要是做隔离的，mount 、IPC 、Network、PID、User、UTS(主机名和域名)

CGroup 主要是对资源隔离做限制的

## 容器使用top free 

如何在容器内查看自己的系统信息，因为/proc文件系统不能识别cgroup最资源限制，所以而容器挂在的是系统的proc文件，所容器中使用top，free等命令看到的系统负载的，使用 `lxcfs`，

## 容器文件系统

文件系统隔离，rootfs,是整个操作系统文件和目录

层的概念，联合文件系统，层最终也是体现在文件系统中，启动的最终容器会把层挂在到一个挂在点、

镜像的每一层都会对应docker 目录/data/docker/overlay2/ 里面的一个目录。

基础层可以理解为是一个小型的ISO的中的除内核外的所有文件。

最终会把所有层合并和一个最终层也就是最终运行时的MergedDir 指定的目录

/data/docker/overlay2/id/diff  这一层和之前层的差别

可以使用docker inspect 镜像名称  查看镜像对应的层目录

```json
GraphDriver": {
            "Data": {
                "LowerDir": "/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e-init/diff:/data/docker/overlay2/e8002e028f4adfb2a57f7109ab9fc83085a00f4e8153714d80549cdc0c5480dc/diff:/data/docker/overlay2/f453f496ea5d000e8db7e9db59ab73d9dd6cfa4100af3bd67ed8eb4b92471ae3/diff:/data/docker/overlay2/04efa1bd8ccd7dba5c2522e36abf4b81ebca946a009e3c9f2a9547d953694de0/diff:/data/docker/overlay2/dada1ccfb2b16d621a47dc3139ac3c4a01ac181e0f477860fe650cf59d5e6589/diff:/data/docker/overlay2/11bebfb5d13ded13395fc310b75e658a8cae33594613fbfa01b2758acfc75aeb/diff",
                "MergedDir": "/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e/merged",
                "UpperDir": "/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e/diff",
                "WorkDir": "/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e/work"
            },
            "Name": "overlay2"
        },
```

https://dker.ru/docs/docker-engine/user-guide/docker-storage-drivers/overlayfs-storage-in-practice/

OverlayFS是类似于AUFS 的现代 联合文件系统，联合文件系统可以把多个目录中内容合并挂载到一个目录中。

存储驱动程序利用了OverlayFS功能来构建和管理映像和容器的磁盘上结构。

overlay2存储驱动程序比overlay inode利用率要高效得多

#### CopyData

docker run -v /home:/test 的时候，容器镜像里的 /test 目录下本来就有内容的话，你会发现，在宿主机的 /home 目录下，也会出现这些内容

## 容器组成

rootfs+namespace +Ggroup 

静态部分 一组联合的底层文件 /data/docker/overlay2/id/ 的rootfs 是容器的image ,容器的静态视图

namespace +Ggroup 构成的隔离环境，这一部分是容器的运行时，是容器的动态视图。

容器启动后，会在在生成1个新的层，可以使用`docker inspect 容器id` 查看

```shell
"GraphDriver": {
            "Data": {
                "LowerDir": "/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e-init/diff:/data/docker/overlay2/e8002e028f4adfb2a57f7109ab9fc83085a00f4e8153714d80549cdc0c5480dc/diff:/data/docker/overlay2/f453f496ea5d000e8db7e9db59ab73d9dd6cfa4100af3bd67ed8eb4b92471ae3/diff:/data/docker/overlay2/04efa1bd8ccd7dba5c2522e36abf4b81ebca946a009e3c9f2a9547d953694de0/diff:/data/docker/overlay2/dada1ccfb2b16d621a47dc3139ac3c4a01ac181e0f477860fe650cf59d5e6589/diff:/data/docker/overlay2/11bebfb5d13ded13395fc310b75e658a8cae33594613fbfa01b2758acfc75aeb/diff",
                "MergedDir": "/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e/merged",
                "UpperDir": "/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e/diff",
                "WorkDir": "/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e/work"
            },
            "Name": "overlay2"
        },
```

/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e-init/diff 就是容器的基础层启动的时候和之前层不一样的配置。

/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e/diff

这个是容器运行时候，写的内容都在这个层里面，只是改变的内容。

/data/docker/overlay2/35552d0873fa270bb00d57c0638abea5425c2d5339013f7412a70f9453f4494e/merged 是当前容器的根目录。也就是rootfs的目录

/data/docker/containers/6b2effb87780ceefa9426bcfdc27ff16c63773fb5dfad13567e6021bfdf6830f

这个里面报错的容器镜像的元信息和标准输出日志文件等。

## docker tini

docker-entrypoint.sh

```shell
#! /bin/bash
set -e
service=`ls /app`
JAVA_OPTS=$JAVA_OPTS
for i in `/usr/bin/env |  grep "FY_JAVA_OPTS" | awk -F "=" '{print $1}'`;do
   JAVA_OPTS=$JAVA_OPTS' '`eval echo '$'"$i"`
done
LOG_PATH="$LOG_PATH/$POD_NAME"
exec java $JAVA_OPTS -jar -Dlog.path=$LOG_PATH /app/$service
```

