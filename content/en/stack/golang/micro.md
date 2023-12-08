---
author: "wangjinbao"
title: "kratos微服务框架"
date: 2022-12-03 00:00:01
description: "一套轻量级 Go 微服务框架，包含大量微服务相关框架及工具"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags: 
- golang
- categories:




---

# kratos

## 优势

| 比对项       | 内容                                                       |
|-----------|----------------------------------------------------------|
| 框架名	      | kratos                                                   |
| 维护公司	     | Bilibli                                                  |
| 项目地址      | https://github.com/go-kratos/kratos                      |
| star数	    | 21.7k                                                    |
| 开源时间	     | 2019年                                                    |
| 服务治理      | 服务注册/发现、负载均衡、熔断、限流、异常恢复、监控、链路跟踪、日志等                      |
| 传输协议	     | gRPC、HTTP                                                |
| 服务发现拓展支持	 | nacos、consul、etcd、polaris、kubernetes、discovery、zookeeper |
| API定义	    | 仅支持Protobuf                                              |
| 框架原则特点    | 简单、通用、高效、稳定、健壮、高性能、扩展性、容错性、工具                            |

## 特性
+ APIS: 协议通信以 HTTP/gRPC 为基础，通过 Protobuf 进行定义
+ Errors: 通过 Protobuf 的 Enum 作为错误码定义，以及工具生成判定接口
+ Metadata: 在协议通信 HTTP/gRPC 中，通过 Middleware 规范化服务元信息传递
+ Config: 支持多数据源方式，进行配置合并铺平，通过 Atomic 方式支持动态配置
+ Logger: 标准日志接口，可方便集成三方 log 库，并可通过 fluentd 收集日志
+ Metrics: 统一指标接口，可以实现各种指标系统，默认集成 Prometheus
+ Tracing: 遵循 OpenTelemetry 规范定义，以实现微服务链路追踪
+ Encoding: 支持 Accept 和 Content-Type 进行自动选择内容编码
+ Transport: 通用的 HTTP/gRPC 传输层，实现统一的 Middleware 插件支持
+ Registry: 实现统一注册中心接口，可插件化对接各种注册中心

## kratos-layout方案
kratos官方提供了一套目录结构方案
```shell
  .
├── Dockerfile  
├── LICENSE
├── Makefile  
├── README.md
├── api // 下面维护了微服务使用的proto文件以及根据它们所生成的go文件
│   └── helloworld
│       └── v1
│           ├── error_reason.pb.go
│           ├── error_reason.proto
│           ├── error_reason.swagger.json
│           ├── greeter.pb.go
│           ├── greeter.proto
│           ├── greeter.swagger.json
│           ├── greeter_grpc.pb.go
│           └── greeter_http.pb.go
├── cmd  // 整个项目启动的入口文件
│   └── server
│       ├── main.go
│       ├── wire.go  // 我们使用wire来维护依赖注入
│       └── wire_gen.go
├── configs  // 这里通常维护一些本地调试用的样例配置文件
│   └── config.yaml
├── generate.go
├── go.mod
├── go.sum
├── internal  // 该服务所有不对外暴露的代码，通常的业务逻辑都在这下面，使用internal避免错误引用
│   ├── biz   // 业务逻辑的组装层，类似 DDD 的 domain 层，data 类似 DDD 的 repo，而 repo 接口在这里定义，使用依赖倒置的原则。
│   │   ├── README.md
│   │   ├── biz.go
│   │   └── greeter.go
│   ├── conf  // 内部使用的config的结构定义，使用proto格式生成
│   │   ├── conf.pb.go
│   │   └── conf.proto
│   ├── data  // 业务数据访问，包含 cache、db 等封装，实现了 biz 的 repo 接口。我们可能会把 data 与 dao 混淆在一起，data 偏重业务的含义，它所要做的是将领域对象重新拿出来，我们去掉了 DDD 的 infra层。
│   │   ├── README.md
│   │   ├── data.go
│   │   └── greeter.go
│   ├── server  // http和grpc实例的创建和配置
│   │   ├── grpc.go
│   │   ├── http.go
│   │   └── server.go
│   └── service  // 实现了 api 定义的服务层，类似 DDD 的 application 层，处理 DTO 到 biz 领域实体的转换(DTO -> DO)，同时协同各类 biz 交互，但是不应处理复杂逻辑
│       ├── README.md
│       ├── greeter.go
│       └── service.go
└── third_party  // api 依赖的第三方proto
    ├── README.md
    ├── google
    │   └── api
    │       ├── annotations.proto
    │       ├── http.proto
    │       └── httpbody.proto
    └── validate
        ├── README.md
        └── validate.proto
```

## 安装
### 依赖环境：
1. go
2. protoc
```shell
protoc --version
libprotoc 25.0
```
3. 安装protobuf的go扩展工具 protoc-gen-go
```shell
go install google.golang.org/protobuf/cmd/protoc-gen-go
```
4. 建议开启GO111MODULE
```shell
go env -w GO111MODULE=on
```
5. 安装kratos脚手架
su root
```shell
GOPROXY=https://goproxy.io,direct go install github.com/go-kratos/kratos/cmd/kratos/v2@latest && kratos upgrade

```
6. 安装kratos框架
切换root，因数会把kratos 放到bin目录
`go install github.com/go-kratos/kratos/cmd/kratos/v2@latest`
验证：
```shell
kratos -v
kratos version v2.7.1
```
## 创建项目

`kratos new helloworld -r https://gitee.com/go-kratos/kratos-layout.git`
```shell
kratos new helloworld -r https://gitee.com/go-kratos/kratos-layout.git
🚀 Creating service helloworld, layout repo is https://gitee.com/go-kratos/kratos-layout.git, please wait a moment.

正克隆到 '/Users/wangdante/.kratos/repo/gitee.com/go-kratos/kratos-layout@main'...

CREATED helloworld/.gitignore (552 bytes)
CREATED helloworld/Dockerfile (459 bytes)
CREATED helloworld/LICENSE (1066 bytes)
CREATED helloworld/Makefile (2525 bytes)
CREATED helloworld/README.md (1062 bytes)
CREATED helloworld/api/helloworld/v1/error_reason.pb.go (4991 bytes)
CREATED helloworld/api/helloworld/v1/error_reason.proto (290 bytes)
CREATED helloworld/api/helloworld/v1/greeter.pb.go (8074 bytes)
CREATED helloworld/api/helloworld/v1/greeter.proto (678 bytes)
CREATED helloworld/api/helloworld/v1/greeter_grpc.pb.go (3560 bytes)
CREATED helloworld/api/helloworld/v1/greeter_http.pb.go (2139 bytes)
CREATED helloworld/cmd/helloworld/main.go (1744 bytes)
CREATED helloworld/cmd/helloworld/wire.go (607 bytes)
CREATED helloworld/cmd/helloworld/wire_gen.go (1069 bytes)
CREATED helloworld/configs/config.yaml (266 bytes)
CREATED helloworld/go.mod (1053 bytes)
CREATED helloworld/go.sum (18535 bytes)
CREATED helloworld/internal/biz/README.md (6 bytes)
CREATED helloworld/internal/biz/biz.go (128 bytes)
CREATED helloworld/internal/biz/greeter.go (1236 bytes)
CREATED helloworld/internal/conf/conf.pb.go (20782 bytes)
CREATED helloworld/internal/conf/conf.proto (761 bytes)
CREATED helloworld/internal/data/README.md (7 bytes)
CREATED helloworld/internal/data/data.go (473 bytes)
CREATED helloworld/internal/data/greeter.go (835 bytes)
CREATED helloworld/internal/server/grpc.go (826 bytes)
CREATED helloworld/internal/server/http.go (831 bytes)
CREATED helloworld/internal/server/server.go (150 bytes)
CREATED helloworld/internal/service/README.md (10 bytes)
CREATED helloworld/internal/service/greeter.go (688 bytes)
CREATED helloworld/internal/service/service.go (136 bytes)
CREATED helloworld/openapi.yaml (1130 bytes)
CREATED helloworld/third_party/README.md (14 bytes)
CREATED helloworld/third_party/errors/errors.proto (411 bytes)
CREATED helloworld/third_party/google/api/annotations.proto (1051 bytes)
CREATED helloworld/third_party/google/api/client.proto (3395 bytes)
CREATED helloworld/third_party/google/api/field_behavior.proto (3011 bytes)
CREATED helloworld/third_party/google/api/http.proto (15140 bytes)
CREATED helloworld/third_party/google/api/httpbody.proto (2671 bytes)
CREATED helloworld/third_party/google/protobuf/any.proto (5909 bytes)
CREATED helloworld/third_party/google/protobuf/api.proto (7734 bytes)
CREATED helloworld/third_party/google/protobuf/compiler/plugin.proto (8754 bytes)
CREATED helloworld/third_party/google/protobuf/descriptor.proto (38497 bytes)
CREATED helloworld/third_party/google/protobuf/duration.proto (4895 bytes)
CREATED helloworld/third_party/google/protobuf/empty.proto (2429 bytes)
CREATED helloworld/third_party/google/protobuf/field_mask.proto (8185 bytes)
CREATED helloworld/third_party/google/protobuf/source_context.proto (2341 bytes)
CREATED helloworld/third_party/google/protobuf/struct.proto (3779 bytes)
CREATED helloworld/third_party/google/protobuf/timestamp.proto (6459 bytes)
CREATED helloworld/third_party/google/protobuf/type.proto (6126 bytes)
CREATED helloworld/third_party/google/protobuf/wrappers.proto (4042 bytes)
CREATED helloworld/third_party/openapi/v3/annotations.proto (2195 bytes)
CREATED helloworld/third_party/openapi/v3/openapi.proto (22082 bytes)
CREATED helloworld/third_party/validate/README.md (81 bytes)
CREATED helloworld/third_party/validate/validate.proto (31270 bytes)

🍺 Project creation succeeded helloworld
💻 Use the following command to start the project 👇:

$ cd helloworld
$ go generate ./...
$ go build -o ./bin/ ./... 
$ ./bin/helloworld -conf ./configs

                        🤝 Thanks for using Kratos
        📚 Tutorial: https://go-kratos.dev/docs/getting-started/start

```
 -r 指定源


```shell
go mod tidy
go mod tidy: go.mod file indicates go 1.18, but maximum supported version is 1.17
```
切换到go 1.18版本

```shell
go mod tidy
```
查看makefile:
```shell
make help
sudo make init
sudo make api
```
运行项目
```shell
kratos run 
# 修改配置文件 config.yaml 端口
2023/11/21 19:29:48 maxprocs: Leaving GOMAXPROCS=8: CPU quota undefined
DEBUG msg=config loaded: config.yaml format: yaml
INFO ts=2023-11-21T19:29:48+08:00 caller=http/server.go:302 service.id=wangdeMacBook-Pro.local service.name= service.version= trace.id= span.id= msg=[HTTP] server listening on: [::]:8022
INFO ts=2023-11-21T19:29:48+08:00 caller=grpc/server.go:205 service.id=wangdeMacBook-Pro.local service.name= service.version= trace.id= span.id= msg=[gRPC] server listening on: [::]:9022

```

打包项目
```shell
$go build -o ./bin/ ./...
./helloworld 
2023/12/05 11:05:26 maxprocs: Leaving GOMAXPROCS=8: CPU quota undefined
panic: stat ../../configs: no such file or directory
cp -r ../configs ../../

./helloworld 
2023/12/05 11:07:05 maxprocs: Leaving GOMAXPROCS=8: CPU quota undefined
DEBUG msg=config loaded: config.yaml format: yaml
INFO ts=2023-12-05T11:07:05+08:00 caller=http/server.go:317 service.id=wangdeMacBook-Pro.local service.name= service.version= trace.id= span.id= msg=[HTTP] server listening on: [::]:8000
INFO ts=2023-12-05T11:07:05+08:00 caller=grpc/server.go:212 service.id=wangdeMacBook-Pro.local service.name= service.version= trace.id= span.id= msg=[gRPC] server listening on: [::]:9006
INFO ts=2023-12-05T11:07:15+08:00 caller=biz/greeter.go:44 service.id=wangdeMacBook-Pro.local service.name= service.version= trace.id= span.id= msg=CreateGreeter: eric
INFO ts=2023-12-05T11:07:16+08:00 caller=biz/greeter.go:44 service.id=wangdeMacBook-Pro.local service.name= service.version= trace.id= span.id= msg=CreateGreeter: eric

```