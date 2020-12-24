## etcd

#### 获取etcd中存放的网段信息

```shell
./etcdctl --endpoints=https://10.10.95.152:2379  --ca-file=../../cert/ca.pem  --cert-file=../cert/etcd.pem --key-file=../cert/etcd-key.pem  ls /atomic.io/network/subnets/
```

#### 获取网段对应的主机信息

```shell
./etcdctl --endpoints=https://10.10.95.152:2379  --ca-file=../../cert/ca.pem  --cert-file=../cert/etcd.pem --key-file=../cert/etcd-key.pem  get /atomic.io/network/subnets/10.200.216.0-21
```

#### 出现问题，修复方法

查看健康状态

```shell
./etcdctl --endpoints=https://10.10.95.153:2379  --ca-file=../../cert/ca.pem  --cert-file=../cert/etcd.pem --key-file=../cert/etcd-key.pem cluster-health
```

查看节点列表

```shell
./etcdctl --endpoints=https://10.10.95.153:2379  --ca-file=../../cert/ca.pem  --cert-file=../cert/etcd.pem --key-file=../cert/etcd-key.pem  member list
```

移除节点

```shell
./etcdctl --endpoints=https://10.10.95.153:2379  --ca-file=../../cert/ca.pem  --cert-file=../cert/etcd.pem --key-file=../cert/etcd-key.pem  member add 80a990ceff3815f0
```

添加节点

```shell
./etcdctl --endpoints=https://10.10.95.153:2379  --ca-file=../../cert/ca.pem  --cert-file=../cert/etcd.pem --key-file=../cert/etcd-key.pem  member add etcd-master1 https://10.10.95.152:2380
```

删除失败节点的work目录

```shell
rm -rf ../../work
```

如果启动参数中时 new 修改成existing

```shell
initial-cluster-state=existing
```

启动查看节点是否健康

