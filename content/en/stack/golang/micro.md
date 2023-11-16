---
author: "wangjinbao"
title: "微服务"
date: 2022-12-03 00:00:01
description: "微服务的精髓：分而治之，合而用之"
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
>PS: <font color="lightgreen">微服务架构 </font> 是一种设计方法;
> 而  <font color="lightgreen">微服务</font> 这是应该指使用这种方法而设计的一个应用。

## 微服务架构
定义：将复杂的系统使用 `组件化` 的方式进行拆分，并使用轻量级 `通讯方式` 进行整合的一种设计方法。

## 微服务
定义：通过这种架构设计方法拆分出来的一个独立的 组件化 的 `小应用` 。

> 精髓："分而治之，合而用之"

### 单体式开发的缺点：
1. 复杂性逐渐变高
2. 技术债务逐渐上升
3. 维护成本大
4. 持续交付周期长
5. 可扩展性差

### 微服务式开发的特点：
1. 单一职责：
不同的服务通过 `"管道"` 方式灵活组合 
2. 轻量级通信：
XML 和 JSON 它和语言无关、平台无关的；学用协议：通信协议：通常基于HTTP
3. 独立性：
高度解耦
4. 进程隔离：
每个服务单独隔离

### 微服务式开发的缺点：
1. 运维要求较高
每个模块出问题时整个个项目运行异常，不好排查问题，运维要求高
2. 分布式的复杂性
3. 接口调整成本高
一旦用户微服务的接口发生大的变动，那么所有依赖它的微服务都要做相应的调整
4. 重复劳动
每个微服务都要一个工具类

### 微服务式开发的优点：
1. 开发简单
没有太多的累赘
2. 快速响应需求变化
能够快速的影响业务的需求变化
3. 随时随地更新
微服务的部署和更新并不会影响 `全局系统` 的正常运行；多实例部署的情况下，每个服务的重启和更新任何时候都可以
4. 系统更加稳定可靠
高可用的分布式环境之中，有配套的 `监控 `和 `调度` 管理机制，并且还可以提供自由伸缩的管理，充分保障了系统的稳定可靠性

## 重要组件
1. <font color="lightgreen">**protobuf** </font>
跨语言，跨平台 通讯方式 protobuf
轻便高效的结构化数据存储格式，平台无关、语言无关、可扩展
2. <font color="lightgreen">**gRPC** </font>
通讯协议 gRPC
3. <font color="lightgreen">**consul** </font>
调度管理服务发现 consul
4. <font color="lightgreen">**micro** </font>
微服务的框架 micro
5. <font color="lightgreen">**docker** </font>
部署 docker

#### 数据交互的格式比较
1. <font color="lightgreen">**json**:</font>
一般的web项目中，最流行的主要还是json，因为浏览器对于json数据支持非常好，有很多内建的函数支持
2. <font color="lightgreen">**xml**:</font>
在webservice中应该最为广泛，但是相比于json，它的数据更冗余，因为需要成对的闭合标签，json使用了键值对的方式，不仅压缩了一定的数据空间，同时也具有可主读性
3. <font color="lightgreen">**protobuf**:</font>
后起之秀，是谷歌开源的一种数据格式，适合高性，对 `响应速度有要求` 的数据传输场景。因为profobuf是二进制数据格式，需要编码和解码。数据本身不具有可读性。因此只能反序列化之后得到真正可读的数据

#### 相对于其它protobuf更具有优势
1. 序列化后体积相比json和XML很小，适合网络传输
2. 支持跨平台多语言
3. 消息格式升级和兼容性还不错
4. 序列化反序列化速度很快，快于json的处理速度

protobuf有如 XML ，不过它 <font color="lightgreen">更小、更快、也更简单</font> 。你可以定义自己的数据结构，然后使用代码生成器生成的代码来读写一个数据结构。
你甚至可以在无需重新部署程序的情况下更新数据结构。只需使用 Protobuf 对数据结构进行一次描述，即可利用各种不同语言或从各种不同数据流中对你的结构化数据轻松读写。
它有一个非常棒的特性，即 <font color="lightgreen">"向后"</font>  兼容性好，人们不必破坏已部署的、依靠"老"数据格式的程序就可以对数据结构进行升级。
Protobuf语义更清晰，无需类似XML解析器的东西（因为Protobuf编译器会将.proto文件编译生成对应的数据访问类似对Protobuf数据进行序列化、反序列化操作）。
Protobuf的编程模式比较友好，简单易学，同时它拥有良好的文档和示例，对于喜欢简单事物的人而言，protobuf比其他的技术更加有吸引力。

## Protobuf安装
### 一、下载 protobuf:
+ 方式一：（本人使用下载zip）
#地址：https://github.com/protocolbuffers/protobuf
本人用的 protobuf-3.0.x.zip
```shell
git clone https://github.com/protocolbuffers/protobuf.git

```
+ 方式二：
#地址：https://github.com/protocolbuffers/protobuf/releases

### 二、安装依赖库
```shell
$ sudo apt-get install autoconf automake libtool curl make g++ unzip libffi-dev -y
```
### 三、安装
```shell
cd protobuf/
./autogen.sh
./configure
make
sudo make install
#刷新共享库，很重要的一步
sudo ldconfig 
#安装时候会比较卡
#成功后验证
protoc -h
```
### 四、获取proto包
#GO 语言的proto API接口
#修改国内镜像
>PS:报错超时
> `go env -w GO111MODULE=on`
> `go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/,direct` 
> 或 `go env -w GOPROXY=https://goproxy.cn,direct`
> 有时会报错:
> `warning: go env -w GOPROXY=... does not override conflicting OS environment variable`
> 解决方法：
> 重设一下 GOPROXY 既可
> <font color="lightgreen">unset GOPROXY</font>

go get命令 参数：
+ **-u**  : 强制使用网络去更新包和它的依赖包
+ **-v**  : 显示执行的命令
+ **-d** : 只下载不安装
+ **-f** : 只有在你包含了 -u 参数的时候才有效，不让 -u 去验证 import 中的每一个都已经获取了，这对于本地 fork 的包特别有用 
+ **-fix** : 在获取源码之后先运行 fix，然后再去做其他的事情 
+ **-t** : 同时也下载需要为运行测试所需要的包
```shell
$ go get -v -u github.com/golang/protobuf/proto
```
### 五、安装protoc-gen-go插件
它是一个go 程序，编译它之后将可执行文件复制到/bin目录

```shell
#安装
 go get -v -u github.com/golang/protobuf/protoc-gen-go

#复制下载的内容
 mkdir -p /go/src/github.com/golang/
 cp -r /go/pkg/mod/github.com/golang/protobuf\@v1.5.3/ /go/src/github.com/golang/
 mv protobuf\@v1.5.3/ protobuf/
#编译
cd $GOPATH/src/github.com/golang/protobuf/protoc-gen-go/
go build

#将生成的 protoc-gen-go 可执行文件，放在/bin目录下
sudo cp protoc-gen-go /bin/

#验证直接执行命令
protoc-gen-go
```

## Protobuf的语法
`。proto` 文件
定义一个消息类型：
```shell
syntax = "proto3" ;

message PandaRequest{
  string name = 1 ; //名字
  int32 shengao = 2 ; //
  repeated int32 tizhong = 3 ;
}

message PandaResponse{
  int32 error = 1 ; //错误号
  string ermessage = 2 ; //错误信息
}
```
## Protobuf编译器
### 1.新建.proto文件：
```shell
mkdir -p /go/src/myproto/proto1/
cd /go/src/myproto/proto1/
vim test1.proto
```
### 2.编辑.proto文件
内容如下：
```go
syntax = "proto3";
option go_package = "./"; // 指定生成的go文件所在path
message PandaRequest{
    string name = 1;
    int32 shengao = 2;
    repeated int32 tizhong = 3;
}
message PandaResponse{
    int32 error = 1;
    string message = 2;
}
```
### 3.编译对应语言的脚本
调用编译器：
```shell
protoc --proto_path=IMPORT_PATH --cpp_out=DST_DIR --python_out=DST_DIR --go_out=DST_DIR path/to/file.proto
# 或者
protoc --go_out=./  *.proto
```
说明：
1. IMPORT_PATH ：
声明了一个 `.proto` 文件所在的解析 `import` 具体目录。如果忽略则使用当前目录。 如果有多个目录则可以多次调用 `--proto_path` ,它们将会顺序的被访问并执行导入。<font color="lightgreen">-I=IMPORT_PATH</font> 是 `--proto_path` 的简化形式。
2. cpp_out ：
输出路径。 `--cpp_out` 在目标目录DST_DIR中产生C++代码。 `--python_out` 在目标目录DST_DIR中产生python代码。`--go_out` 在目标目录DST_DIR中产生GO代码。

### 4.验证并查看脚本
当前目录生产新的脚本：
`test1.pb.go`
内容是go语言语法：
```go                                                     
// Code generated by protoc-gen-go. DO NOT EDIT.
// versions:
//      protoc-gen-go v1.26.0
//      protoc        (unknown)
// source: test1.proto

package __

import (
        protoreflect "google.golang.org/protobuf/reflect/protoreflect"
        protoimpl "google.golang.org/protobuf/runtime/protoimpl"
        reflect "reflect"
        sync "sync"
)

const (
        // Verify that this generated code is sufficiently up-to-date.
        _ = protoimpl.EnforceVersion(20 - protoimpl.MinVersion)
        // Verify that runtime/protoimpl is sufficiently up-to-date.
        _ = protoimpl.EnforceVersion(protoimpl.MaxVersion - 20)
)
-- VISUAL --                                                          48        1,1           Top
```

| .proto Type | C++ Type | Python Type | Go Type |
|-------------|----------|-------------|---------|
| double      | double   | float       | float64 |
| float       | float    | float       | float32 |
| int32       | int32    | int         | int32   |
| bool        | bool     | bool        | bool    |
| string      | string   | str/unicode | string  |
| bytes       | string   | str         | []byte  |

### 有默认值
### 其他消息类型
要在 `.proto` 文件中指定，例如：
```proto
message PersonInfo{
  message Person{
    string name = 1;
    int32 shengo = 2;
    repeated int32 tizhong = 3;
  }
  repeated Person info =1;
}
```
其它父消息类型的外部使用这个消息类型，使用 `.` 链式形式，如：
```proto
message PersonMessage{
    PersonInfo.Person info = 1;
}
```
## 定义服务(Service)
```shell
service SearchService{
  //rpc 服务的函数名 (传入参数) 返回 (返回参数)
  rpc Search (SearchRequest) returns (SearchResponse);
}
```

## gRPC
最直观的使用 `protocol buffer` 的RPC系统是 gRPC

gRPC 是一个高性能、开源和通用的 RPC 框架，面向移动和HTTP/2设计。
特性：双向流、流控、头部压缩、单TCP连接上的多复用请求等待，对移动设备有好，更省电、节省空间占用。
### 测试
#### 1.生成go包
测试生成的proto 的go包
```shell
mkdir -p /go/src/myproto/prototext
cd /go/src/myproto/prototext
vim text.proto
```
内容如下：
```proto
syntax = "proto3";
option go_package = "./";
package prototext;
message Test{
    string name = 1;
    repeated int32 tizhong = 2;
    int32 shengao = 3;
    string motto = 4;
}  
```
#### 2.测试go包
```shell
mkdir -p /go/src/myproto/test
cd /go/src/myproto/test
vim test.go
```
test.go内容：
```go
package main

import(
        "myproto/prototext"
        "fmt"
        "github.com/golang/protobuf/proto"
)

func main(){
   text:=&prototext.Test{
      Name:"wjb",
      Tizhong:[]int32{120,125,160,166},
      Shengao:181,
      Motto:"good study!",
   }

   fmt.Println(text)

   data,err:=proto.Marshal(text)
   if err!=nil{
       fmt.Println("failed")
   }

   fmt.Println(data)

   newtext:=&prototext.Test{}

   proto.Unmarshal(data.newtext)
   if err!=nil{
       fmt.Println("failed")
   }  
   fmt.Println(newtext)                                                                             
   fmt.Println(newtext.Name)                                                                             
   fmt.Println(newtext.Shengao)   
}
```

### RPC
rpc包提供了通过网络或其他I/O连接对一个对象的导出方法的访问。
服务端注册一个对象，使它作为一个服务被暴露，服务的名字是该对象的类型名。
注册之后，对象的导出方法就可以被远程访问。
服务端可以注册多个不同类型的对象（服务），但注册具有相同类型的多个对象是错误的。

只有满足如下标准的方法才能用于远程访问，其余方法会被忽略：
+ 方法是导出的 (首字母大写)
+ 方法有两个参数，都是导出类型或内建类型
+ 方法的第二个参数是指针
+ 方法只有一个error接口类型的返回值

事实上，方法必须看起来像这样：
```go
func (t *T) MethodName(argType T1 , replyType *T2) error
```

#### 实例(服务端+客户端)
```shell
rpc
    ├── client.go
    └── server.go
```
server.go 内容如下：
```go
package main

import (
	"fmt"
	"io"
	"net"
	"net/http"
	"net/rpc"
)

func pandatext(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "hello dante hello panda")
}

/*
+ 方法是导出的 (首字母大写)
+ 方法有两个参数，都是导出类型或内建类型
+ 方法的第二个参数是指针
+ 方法只有一个error接口类型的返回值
*/
type Panda int

//函数关键字 (对象) 函数名 (对端发送过来的内容 ，返回给对端的内容) 错误返回值
func (p *Panda) Getinfo(artType int, replyType *int) error {
	fmt.Println("打印对端发送过来的内容：", artType)

	//修改内容値
	*replyType = artType + 1230

	return nil
}

func main() {
	//页面的请求
	http.HandleFunc("/panda", pandatext)

	//-1-服务端注册一个对象
	pd := new(Panda)
	rpc.Register(pd)
	rpc.HandleHTTP()

	ln, err := net.Listen("tcp", ":10086")
	if err != nil {
		fmt.Println("网络错误")
	}
	http.Serve(ln, nil)
}
```

运行server.go：
```shell
go run server.go
打印对端发送过来的内容： 10086
```

client.go 内容如下：
```go
package main

import (
	"fmt"
	"net/rpc"
)

func main() {
	//建立网络连接
	cli, err := rpc.DialHTTP("tcp", "127.0.0.1:10086")
	if err != nil {
		fmt.Println("网络连接失败")
	}

	var pd int
	/*
		func (client *Client) Call(serviceMethod string, args any, reply any) error
	*/
	//-2-连接服务
	err = cli.Call("Panda.Getinfo", 10086, &pd)
	if err != nil {
		fmt.Println("打call失败")
	}
	fmt.Println("最后的值为：", pd)
}

```
运行client.go：
```shell
go run client.go
最后的值为： 11316
```