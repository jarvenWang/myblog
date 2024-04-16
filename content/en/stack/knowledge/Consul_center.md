---
author: "wangjinbao"
title: "consul服务注册中心"
date: 2023-05-19
description: "consul服务注册中心"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags:
- knowledge
categories:
- consul
---

## 介绍
市面上常用的嗠注册中心有：
+ Eureka
+ Nacos
+ Consul
+ ZooKeeper
+ Etcd
+ CoreDNS

>Eureka2.X版本官方已经停止更新了。
 
Consul是HashiCorp公司推出使用go语言开发的开源工具,，用于实现分布式系统的服务发现与配置。 Consul是 <font color='cyan'>**分布式的**</font> 、<font color='cyan'>**高可用的**</font> 、 <font color='cyan'>**可横向扩展的**</font>。

## consul特性 
+ <font color='cyan'>**Raft算法**</font>：
>PS:Raft实现了和Paxos相同的功能，它将一致性分解为多个子问题：Leader选举（Leader election）、日志同步（Log replication）、安全性（Safety）、日志压缩（Log compaction）、成员变更（Membership change）等
+ <font color='cyan'>**服务发现**</font>：
consul通过DNS或者HTTP接口使服务注册和服务发现变的很容易，一些外部服务，例如saas提供的也可以一样注册。
+ <font color='cyan'>**健康检查**</font> ：
健康检测使consul可以快速的告警在集群中的操作。和服务发现的集成，可以防止服务转发到故障的服务上面。
+ <font color='cyan'>**Key/Value存储**</font> ：
一个用来存储动态配置的系统。提供简单的HTTP接口，可以在任何地方操作。
+ <font color='cyan'>**多数据中心**</font> ：
无需复杂的配置，即可支持任意数量的区域。
+ <font color='cyan'>**支持http和dns协议接口**</font> 
+ <font color='cyan'>**官方提供web管理界面**</font> 

## consul角色
### client:客户端
<font color='cyan'>**无状态**</font>，将 <font color='cyan'>**HTTP和DNS接口**</font> 请求转发给局域网的服务端集群。

### server:服务端
<font color='cyan'>**保存配置信息**</font>，<font color='cyan'>**高可用集群**</font>，每个数据中心的server数量推荐为3个或5个

![/images/docImages/cl1.png](/images/docImages/cl1.png)

## consul工作原理
### 1.服务发现以及注册
当服务 Producer 启动时，会将自己的 IP/host 等信息通过发送请求告知 Consul 、 Consul 接收到 Producer 的注册信息后，每隔10s(默认)会向 Producer 发送一个健康检查的请求，检验 Producer 是否健康。

### 2.服务调用
当 Consumer 请求 Producer 时，会先从 Consul 中拿到存储 Producer 服务的 IP和Port 的临时表（temp table）,从temp table 表中任选一个 Producer 的IP和Port,然后根据这个IP和Port，发送访问请求；temp table表只包含通过了健康检查的 Producer 信息，并且每隔10s（默认）更新。


![/images/docImages/cl2.png](/images/docImages/cl2.png)

## consul安装
官方网站：
https://consul.io/

### 安装consul
1. 方式一：macOS系统：
```shell
brew tap hashicorp/tap
brew install hashicorp/tap/consul
//检查版本号
consul version
```
#### 验证启动
```shell
//启动consul
consul agent -dev

...
2023-11-17T16:25:34.985+0800 [INFO]  agent: Starting server: address=127.0.0.1:8500 network=tcp protocol=http
2023-11-17T16:25:35.025+0800 [INFO]  agent: Started gRPC listeners: port_name=grpc_tls address=127.0.0.1:8503 network=tcp
2023-11-17T16:25:35.025+0800 [INFO]  agent: Started gRPC listeners: port_name=grpc address=127.0.0.1:8502 network=tcp
```
访问浏览器：
http://127.0.0.1:8500/


## kratos微服务注册consul
### 步骤一：引入依赖包
```shell
$go get -u github.com/hashicorp/consul/api
$go get -u github.com/go-kratos/kratos/contrib/registry/consul/v2
$go mod tidy
```
### 步骤二：添加配置config.proto
文件：`website/internal/conf/conf.proto`
添加
```proto
message Registry {
  message Consul {
    string address = 1;
    string scheme = 2;
  }
  Consul consul = 1;
}
```
当前目录下重置pb.go文件：
```shell
$ protoc --proto_path=./internal --go_out=paths=source_relative:./internal ./internal/conf/conf.proto

```

### 步骤三：添加consul 配置信息
```yaml
server:
  http:
    addr: 0.0.0.0:8902
    timeout: 1s
  grpc:
    addr: 0.0.0.0:9902
    timeout: 1s
  env:
    logsystem: "/Users/wangdante/D/kugou/hl/hl_service/backendapi/logs/system/"
    loginfo: "/Users/wangdante/D/kugou/hl/hl_service/backendapi/logs/info/"
  #consul 配置信息
  consul:
    address: 127.0.0.1:8500
    scheme: http
```

### 步骤四：添加consul服务
目录：
`website/internal/server/consul.go`
内容如下：
```go
package server

import (
	"github.com/go-kratos/kratos/contrib/registry/consul/v2"
	"github.com/go-kratos/kratos/v2/log"
	"github.com/go-kratos/kratos/v2/registry"
	consulAPI "github.com/hashicorp/consul/api"
	"github.com/spf13/viper"
	"website/internal/conf"
)

// NewRegistrar new an Consul server.
func NewRegistrar(conf *conf.Registry, logger log.Logger) registry.Registrar {

	c := consulAPI.DefaultConfig()

	c.Address = viper.GetString("server.consul.address")
	//c.Address = "127.0.0.1:8500"
	c.Scheme = viper.GetString("server.consul.scheme")
	//c.Scheme = "http"

	cli, err := consulAPI.NewClient(c)
	if err != nil {
		panic(err)
	}
	r := consul.New(cli, consul.WithHealthCheck(false))
	return r
}
```
### 步骤五：依赖注入consul
目录：`website/internal/server/server.go`
```go
var ProviderSet = wire.NewSet(NewGRPCServer, NewHTTPServer, NewRegistrar) //添加服务NewRegistrar
```
### 步骤六：main.go入口文件引入consul
```go
...
//添加 rr registry.Registrar 
//并注册kratos.Registrar
func newApp(logger log.Logger, gs *grpc.Server, hs *http.Server, rr registry.Registrar) *kratos.App {
	return kratos.New(
		kratos.ID(id),
		kratos.Name(Name),
		kratos.Version(Version),
		kratos.Metadata(map[string]string{}),
		kratos.Logger(logger),
		kratos.Server(
			gs,
			hs,
		),
		kratos.Registrar(rr),
	)
}
...
func main() {
...
    //读取配置信息
	var rc conf.Registry
	if err := c.Scan(&rc); err != nil {
		panic(err)
	}
...
    //注入 &rc
	app, cleanup, err := wireApp(bc.Server, &rc, bc.Data, logger)
...
```
### 步骤七：初始化应用registrar
目录：
`website/cmd/website/wire_gen.go`
```go
...
// wireApp init kratos application.
// 添加 registry *conf.Registry
//添加newApp应用
func wireApp(confServer *conf.Server, registry *conf.Registry, confData *conf.Data, logger log.Logger) (*kratos.App, func(), error) {
...
	registrarServer := server.NewRegistrar(registry, logger)
	app := newApp(logger, grpcServer, httpServer, registrarServer)
...
}
```

目录：
`website/cmd/website/wire.go`
```go
...
// wireApp init kratos application.
// 添加*conf.Registry
func wireApp(*conf.Server, *conf.Registry, *conf.Data, log.Logger) (*kratos.App, func(), error) {
	panic(wire.Build(server.ProviderSet, data.ProviderSet, biz.ProviderSet, service.ProviderSet, newApp))
}
```

### 步骤八：启动
```shell
go mod tidy
go get github.com/google/wire/cmd/wire@latest
go generate ./...

kratos run
```
>PS:可能报错：服务名称重复
> main.go ==> id = Name + "-" + uuid.NewString()

### 验证成功
去浏览器查看：
![/images/docImages/cl3.png](/images/docImages/cl3.png)