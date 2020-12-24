> https://tencentcloudcontainerteam.github.io/2018/11/19/kernel-parameters-and-container/

#### 内核参数

内核中部分参数支持namesapce化，也就是多个容器和主机可以独自设置某些内核参数，目前支持namespace化的sysctl内核参数

```shell
kernel.shm*,
kernel.msg*,
kernel.sem,
fs.mqueue.*,
net.*.
```

如果在容器中修改了不支持的namesapce化的参数，则会影响到其他容器和宿主机

正常情况下启动的容器，是不能在容器里面修改任何系统参数的，因为/proc/sys 是以只读方式挂到容器中里面， 也是和系统使用相同的内核参数

修改容器的内核参数，3中方式

1. -privileged 特权模式

   整个proc目录以rw权限进行挂载

2. /proc/sys 读写挂在到容器中

3. 容器启动的时候加 -sysctl参数

#### unlimit

每个进程都有若干操作系统资源的限制，可以通过/proc/$pid/limits的方式查看

可以通过docker启动参数给容器设置默认的limit限制，默认容器的umlimit是

```shell
[root@4a826d47ba59 nginx]# ulimit -u
core file size          (blocks, -c) unlimited
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 30126
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1048576
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) unlimited
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

也可以给在容器的启动的时候设置启动limit限制

