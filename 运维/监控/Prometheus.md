## prometheus

#### 1、收集数据方式

监控方式主动和被动，

主动主要是推送数据到pushGetway

被动式通过prometheus 主动去拉去监控指标，

被监控的服务指标的暴露，

1. node export  mysql export 等promtheus提高的第三方的指标导出器
2. 第三方程序自己就提供了指标数据，可供promtheus去获取，如 kubernetes、ceph、Linkerd、Kong 

>https://github.com/yunlzheng/prometheus-book
>
>https://songjiayang.gitbooks.io/prometheus/content/alertmanager/others.html

#### 2、配置

```yaml
scrape_interval # Server端抓取数据的时间间隔
scrape_timeout #  数据抓取的超时时间
evaluation_interval # 评估报警规则的时间间隔
```

## alertmanager

```yaml
global:
  resolve_timeout: 5m
route:
  group_by: ['isDown']
  group_wait: 10s  # 当一个新的报警分组被创建后，需要等待至少group_wait时间来初始化通知，  这种方式可以确保您能。有足够的时间为同一分组来获取多个警报，然后一起触发这个报警信息
  group_interval: 10s #当第一个报警发送后，等待'group_interval'时间来发送新的一组报警信息
  repeat_interval: 1h #如果一个报警信息已经发送成功了，等待'repeat_interval'时间来重新发送他们
  receiver: 'web.hook'
receivers:
- name: 'web.hook'
  webhook_configs:
  - url: 'http://127.0.0.1:8080/'
```

## export

#### nginx_export

修改nginx_export 支持自动注册,在main包下新建一个文件Register_consul.go

```go
package main

import (
	"flag"
	"github.com/denisbrodbeck/machineid"
	consulapi "github.com/hashicorp/consul/api"
	"log"
	"strconv"
	"strings"
)

var (
	consul_addr = flag.String(
		"consul.addr",
		"",
		"Set  consul addr IP:PORT ",
	)
	consul_service = flag.String(
		"consul.service",
		"node_exporter",
		"设置注册到consul 中的指定Services中，默认为node_exporter",
	)
	consul_serverId = flag.String(
		"consul.ServerId",
		hostName,
		"设置注册到consul的服务名称,默认为主机名称_主机唯一ID",
	)
	consul_reg_addr = flag.String(
		"consul.regAddr",
		"",
		"设置注册到consul中的本地的地址  IP:PORT,prometheus 会通过这个这个地址获取监控数据 ",
	)
)

var ConsulClient *consulapi.Client
var config *consulapi.Config

//func Unregservice_consul()  {
//	err:=ConsulClient.Agent().ServiceDeregister(ServiceID)
//	if err!=nil{
//		level.Error(logger).Log("err", err)
//	}
//}

func Register_consul(consulAddr, consul_service, serverID, RegAddr string) {
	id, err := machineid.ID()
	if err != nil {
		log.Fatalf("获取服务器唯一ID失败", err)
	}
	if RegAddr == "" {
		log.Fatal("请指定RegAddr参数")
	}
	config = consulapi.DefaultConfig()
	config.Address = consulAddr
	Client, err := consulapi.NewClient(config)
	if err != nil {
		log.Fatal("err", err)
	}
	ConsulClient = Client
	check := consulapi.AgentServiceCheck{}
	check.Interval = "5s"
	check.HTTP = "http://" + RegAddr
	addr_port := strings.Split(RegAddr, ":")
	print(addr_port)
	ServerPort, _ := strconv.Atoi(addr_port[1])
	ServerAddr := addr_port[0]
	reg := consulapi.AgentServiceRegistration{
		ID:      hostName + id,
		Name:    consul_service,
		Tags:    []string{hostName},
		Port:    ServerPort,
		Address: ServerAddr,
		Check:   &check,
	}
	err = ConsulClient.Agent().ServiceRegister(&reg)
	if err != nil {
		log.Fatal("err", err)
	} else {
		log.Println("注册到: " + consulAddr + " 成功")
	}
}
```

main.go 启动http监听之前或者之后添加如下

```go
if *consul_addr!=""{
    Register_consul(*consul_addr,*consul_service,*consul_serverId,*consul_reg_addr)
}
```

#### node_export 

修改nginx_export 支持自动注册,在main包下新建一个文件Register_consul.go

```go
package main

import (
	"github.com/go-kit/kit/log/level"
	consulapi "github.com/hashicorp/consul/api"
	"strconv"
	"strings"
)

var ConsulClient *consulapi.Client
var config *consulapi.Config

//func Unregservice_consul()  {
//	err:=ConsulClient.Agent().ServiceDeregister(ServiceID)
//	if err!=nil{
//		level.Error(logger).Log("err", err)
//	}
//}



func Register_consul(consulAddr, consul_service,serverID,RegAddr string) {
	if(RegAddr ==""){
		level.Error(logger).Log("请指定RegAddr参数")
	}
	config=consulapi.DefaultConfig()
	config.Address=consulAddr
	Client,err := consulapi.NewClient(config)
	if err!=nil{
		level.Error(logger).Log("err", err)
	}
	ConsulClient=Client
	check:=consulapi.AgentServiceCheck{}
	check.Interval="5s"
	check.HTTP="http://"+RegAddr
	addr_port:=strings.Split(RegAddr,":")
	print(addr_port)
	ServerPort, _ :=strconv.Atoi(addr_port[1])
	ServerAddr:=addr_port[0]
	reg:=consulapi.AgentServiceRegistration{
		ID:                serverID,
		Name:              consul_service,
		Tags:              []string{hostName},
		Port:              ServerPort,
		Address:           ServerAddr,
		Check:&check,
	}
	err=ConsulClient.Agent().ServiceRegister(&reg)
	if err!=nil{
		level.Error(logger).Log("err", err)
	}else {
		level.Info(logger).Log("注册到: "+consulAddr+" 成功")
	}
}
```

nodex_export 接受命令行参数使用的是kingpin，且在main函数中定义这，所以需要修改main函数的接受命令行参数的内容

```go
		consul_addr = kingpin.Flag(
			"consul.addr",
			"Set  consul addr IP:PORT ",
		).Default("").String()
		consul_service = kingpin.Flag(
			"consul.service",
			"设置注册到consul 中的指定Services中，默认为node_exporter ",
		).Default("node_exporter").String()
		consul_serverId = kingpin.Flag(
			"consul.ServerId",
			"设置注册到consul的服务名称,默认为主机名称_主机唯一ID",
		).Default(ServerID).String()
		consul_reg_addr = kingpin.Flag(
			"consul.regAddr",
			"设置注册到consul中的本地的地址  IP:PORT ",
		).Default("").String()
```

添加自动注册，也是main函数中，启动http之前或者之后

```go
	if *consul_addr!=""{
		Register_consul(*consul_addr,*consul_service,*consul_serverId,*consul_reg_addr)
	}
```

