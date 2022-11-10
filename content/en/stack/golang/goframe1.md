---
author: "wangjinbao"
title: "goframe框架-1"
date: 2021-01-11T12:00:06+09:00
description: "go语言起源、安装运行环境、编辑器、集成等"
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


## 核心组件
### 手动编译安装
这是万能的安装方式：
```shell
git clone https://github.com/gogf/gf && cd gf/cmd/gf && go install
```
### 验证安装成功
```shell
$ gf -v
GoFrame CLI Tool v2.2.1, https://goframe.org
GoFrame Version: cannot find goframe requirement in go.mod
CLI Installed At: /usr/local/go/bin/gf
Current is a custom installed version, no installation information.
```

### 创建项目模板
```shell
gf init demo -u
```

### 运行项目模板
项目模板可以执行以下命令运行：
```shell
cd demo && gf run main.go
```
会生成demo目录，里面就是个完整的项目
### 把新项目独立出来
结构如下：
```shell
├── Makefile
├── README.MD
├── api
│   └── v1
│       ├── hello.go
│       └── user.go
├── go.mod
├── go.sum
├── hack
│   └── config.yaml
├── internal
│   ├── cmd
│   │   └── cmd.go
│   ├── consts
│   │   └── consts.go
│   ├── controller
│   │   ├── hello.go
│   │   └── user.go
│   ├── dao
│   │   ├── document.go
│   │   └── internal
│   │       └── document.go
│   ├── logic
│   │   └── middleware
│   ├── model
│   │   ├── do
│   │   │   └── document.go
│   │   └── entity
│   │       └── document.go
│   ├── packed
│   │   └── packed.go
│   └── service
├── main
├── main.go
├── manifest
│   ├── config
│   │   └── config.yaml
│   ├── deploy
│   │   └── kustomize
│   │       ├── base
│   │       │   ├── deployment.yaml
│   │       │   ├── kustomization.yaml
│   │       │   └── service.yaml
│   │       └── overlays
│   │           └── develop
│   │               ├── configmap.yaml
│   │               ├── deployment.yaml
│   │               └── kustomization.yaml
│   └── docker
│       ├── Dockerfile
│       └── docker.sh
├── resource
│   ├── i18n
│   ├── public
│   │   ├── html
│   │   ├── plugin
│   │   └── resource
│   │       ├── css
│   │       ├── image
│   │       └── js
│   └── template
└── utility

```
### 注册路由
+ 查看internal/cmd/cmd.go，添加对象
```golang
...
s := g.Server()
s.Group("/api", func(group *ghttp.RouterGroup) {
	group.Middleware(ghttp.MiddlewareHandlerResponse)
	group.Bind(
		controller.Hello,
		controller.User,
	)
})
s.Run()
...
```
+ 添加控制器internal/controller/user.go
```golang
 package controller

import (
	"context"

	"github.com/gogf/gf/v2/frame/g"

	"ecf/api/v1"
)

var (
    User = cUser{}
)

type cUser struct{}

func (c *cUser) AuthLogin(ctx context.Context, req *v1.UserReq) (res *v1.UserRes, err error) {
    g.RequestFromCtx(ctx).Response.Writeln("jarvenwang--AuthLogin")
    return
}
```
+ 添加api/v1/user.go,添加 请求：UserReq，响应：UserRes
```golang
type UserReq struct {
    //path：路由地址
    //method：post
    //summary:概括
    g.Meta `path:"/auth-login" tags:"AuthLogin" method:"post" summary:"登录"`
}
type UserRes struct {
    g.Meta `mime:"text/html" example:"string"`
}
```

### 连接数据库
+ 修改工具配置文件 ：hack/config.yaml
```golang
# CLI tool, only in development environment.
# https://goframe.org/pages/viewpage.action?pageId=3673173
gfcli:
  gen:
    dao:
      - link:            "mysql:root:654321@tcp(127.0.0.1:3306)/TME_ECF_DEV"
        tables:          "ecf_document"
        removePrefix:    "ecf_"
        jsonCase:        "Snake" //指定model中生成的数据实体对象中json标签名称规则，参数不区分大小写
        descriptionTag:  true
        noModelComment:  true
```
jsonCase 默认值 CamelLower 
|名称|必须|默认值|说明|示例|
|-------:|-------:|-------:|-------:|-------:|
|jsonCase |    |CamelLower  |指定model中生成的数据实体对象中json标签名称规则，参数不区分大小写。参数可选为：Camel、CamelLower、Snake、SnakeScreaming、SnakeFirstUpper、Kebab、KebabScreaming。具体介绍请参考命名行帮助示例。   |Snake：any_kind_of_string|
|removePrefix|||删除数据表的指定前缀名称。多个前缀以,号分隔。|	gf_|
|descriptionTag||false|用于指定是否为数据模型结构体属性增加desription的标签，内容为对应的数据表字段注释。|true|
|noModelComment||false|用于指定是否关闭数据模型结构体属性的注释自动生成，内容为数据表对应字段的注释。|true|
+ 执行命令 gf gen dao 生成数据对象
```shell
$gf gen dao
generated: internal/dao/internal/document.go
...
done!

```
数据库业务配置：
manifest/config/config.yaml
```golang
database:
  default:
    link: "mysql:root:654321@tcp(127.0.0.1:3306)/TME_ECF_DEV"
    debug: true
```
```golang
//控制器中添加
dao.Document.Ctx(ctx).Data(do.Document{
        Setid:     "TME01",
		Deptid:    "10000690",
		Grade:     1,
		EcfName:   "档案名称",
		EcfCode:   "ECFakfjdklfjdk",
		Key1:      "Key1",
		Key2:      "Key2",
		Key3:      "Key3",
		FileCode:  "FILEadflksajdlkf",
		Class:     "入职",
		Style:     "入职简历",
		CreatedAt: time.Now().Unix(),
}).Insert()
```
以上代码可能会报错，因为数据库驱动没有下载：
安装mysql 驱动：
```shell
go get -u github.com/gogf/gf/contrib/drivers/mysql/v2
```

### 区分环境配置文件
#### 配置管理-文件配置
默认配置文件
配置对象我们推荐使用单例方式获取，单例对象将会按照文件后缀toml/yaml/yml/json/ini/xml/properties文自动检索配置文件。默认情况下会自动检索配置文件config.toml/yaml/yml/json/ini/xml/properties并缓存，配置文件在外部被修改时将会自动刷新缓存。

如果想要自定义文件格式，可以通过SetFileName方法修改默认读取的配置文件名称（如：default.yaml, default.json, default.xml等等）。例如，我们可以通过以下方式读取default.yaml配置文件中的数据库database配置项。

```golang
// 设置默认配置文件，默认读取的配置文件设置为 default.yaml
g.Cfg().GetAdapter().(*gcfg.AdapterFile).SetFileName("default.yaml")

// 后续读取时将会读取到 default.yaml 配置文件内容
g.Cfg().Get(ctx, "database")
```
文件可以是一个具体的文件名称或者完整的文件绝对路径。
我们可以通过多种方式修改默认文件名称：

+ 通过配置管理方法SetFileName修改。
+ 修改命令行启动参数 - gf.gcfg.file。
+ 修改指定的环境变量 - GF_GCFG_FILE。

假如我们的执行程序文件为main，那么可以通过以下方式修改配置管理器的配置文件目录(Linux下)：

1 通过单例模式

```shell
g.Cfg().GetAdapter().(*gcfg.AdapterFile).SetFileName("default.yaml")
```
2 通过命令行启动参数
```shell
./main --gf.gcfg.file=config.prod.toml
```
3 通过环境变量（常用在容器中）
启动时修改环境变量：
```shell
GF_GCFG_FILE=config.prod.toml; ./main
```
使用genv模块来修改环境变量：
```shell
genv.Set("GF_GCFG_FILE", "config.prod.toml")
```
实际操作：
+ 步骤一：
  在manifest/config/目录下新增三个配置文件 ：
  dev.yaml
  uat.yaml
  prod.yaml
```golang
dev.yaml文件内容：
server:
  address:     ":8002"
  openapiPath: "/api.json"
  swaggerPath: "/swagger"

logger:
  level : "all"
  stdout: true

database:
  default:
    link: "mysql:root:654321@tcp(127.0.0.1:3306)/TME_ECF_DEV"
    debug: true
```
+ 步骤二：
  在main.go文件修改如下：
```golang
func main() {
	genv.Set("env", "dev")
	//环境配置文件
	envfile := genv.Get("env").String() + ".yaml"
	//环境配置文件
	g.Cfg().GetAdapter().(*gcfg.AdapterFile).SetFileName(envfile)

	cmd.Main.Run(gctx.New())
}
```


#### 配置目录：
目录配置方法
gcfg配置管理器支持非常灵活的多目录自动搜索功能，通过SetPath可以修改目录管理目录为唯一的目录地址，同时，我们推荐通过AddPath方法添加多个搜索目录，配置管理器底层将会按照添加目录的顺序作为优先级进行自动检索。直到检索到一个匹配的文件路径为止，如果在所有搜索目录下查找不到配置文件，那么会返回失败。

默认目录配置
gcfg配置管理对象初始化时，默认会自动添加以下配置文件搜索目录：

当前工作目录及其下的config、manifest/config目录：例如当前的工作目录为/home/www时，将会添加：
a. /home/www
b. /home/www/config
c. /home/www/manifest/config
当前可执行文件所在目录及其下的config、manifest/config目录：例如二进制文件所在目录为/tmp时，将会添加：
a. /tmp
b. /tmp/config
c. /tmp/manifest/config
当前main源代码包所在目录及其下的config、manifest/config目录(仅对源码开发环境有效)：例如main包所在目录为/home/john/workspace/gf-app时，将会添加：
a. /home/john/workspace/gf-app
b. /home/john/workspace/gf-app/config
c. /home/john/workspace/gf-app/manifest/config
默认目录修改
注意这里修改的参数必须是一个目录，不能是文件路径。

我们可以通过以下方式修改配置管理器的配置文件搜索目录，配置管理对象将会只在该指定目录执行配置文件检索：

+ 通过配置管理器的SetPath方法手动修改；
+ 修改命令行启动参数 - gf.gcfg.path；
+ 修改指定的环境变量 - GF_GCFG_PATH；

假如我们的执行程序文件为main，那么可以通过以下方式修改配置管理器的配置文件目录(Linux下)：

通过单例模式

```shell
g.Cfg().GetAdapter().(*gcfg.AdapterFile).SetPath("/opt/config")
```
通过命令行启动参数
```shell
./main --gf.gcfg.path=/opt/config/
```
通过环境变量（常用在容器中）
启动时修改环境变量：
```shell
GF_GCFG_PATH=/opt/config/; ./main
```
使用genv模块来修改环境变量：
```shell
genv.Set("GF_GCFG_PATH", "/opt/config")
```

### 部署打包二进制执行文件
#### 交叉编译-build
支持把配置文件打包到执行文件中
编译配置文件：
build支持同时从命令行以及配置文件指定编译参数、选项。GoFrame框架的所有组件及所有生态项目都是使用的同一个配置管理组件，默认的配置文件以及配置使用请参考章节 配置管理。以下是一个简单的配置示例供参考（以config.yaml为例）：
```golang
gfcli:
  build:
    name:     "gf"
    #当前系统架构，例如：386,amd64,arm
    arch:     "all"
    system:   "all"
    mod:      "none"
    #可以不用，一般项目部署附带目录一起上传即可，不用打包到执行文件中，不方便修改配置参数
    packSrc:  "resource,manifest" 
    version:  "v1.0.0"
    output:   "./bin"
    extra:    ""
#我本地的如下：
  build:
    name:     "ecf"
    arch:     "amd64"
    system:   "linux"
    mod:      "none"
#    packSrc:  "resource,manifest"
    version:  "v1.0.0"
#    output:   "./bin"
#    extra:    ""
```

### 日志配置
日志组件支持配置文件，当使用g.Log(单例名称)获取Logger单例对象时，将会自动通过默认的配置管理对象获取对应的Logger配置。默认情况下会读取logger.单例名称配置项，当该配置项不存在时，将会读取默认的logger配置项。配置项请参考配置对象结构定义：https://pkg.go.dev/github.com/gogf/gf/v2/os/glog#Config

完整配置文件配置项及说明如下，其中配置项名称不区分大小写：
```golang
logger:
  path:                  "/var/log/"   # 日志文件路径。默认为空，表示关闭，仅输出到终端
  file:                  "{Y-m-d}.log" # 日志文件格式。默认为"{Y-m-d}.log"
  prefix:                ""            # 日志内容输出前缀。默认为空
  level:                 "all"         # 日志输出级别
  ctxKeys:               []            # 自定义Context上下文变量名称，自动打印Context的变量到日志中。默认为空
  header:                true          # 是否打印日志的头信息。默认true
  stdout:                true          # 日志是否同时输出到终端。默认true
  rotateSize:            0             # 按照日志文件大小对文件进行滚动切分。默认为0，表示关闭滚动切分特性
  rotateExpire:          0             # 按照日志文件时间间隔对文件滚动切分。默认为0，表示关闭滚动切分特性
  rotateBackupLimit:     0             # 按照切分的文件数量清理切分文件，当滚动切分特性开启时有效。默认为0，表示不备份，切分则删除
  rotateBackupExpire:    0             # 按照切分的文件有效期清理切分文件，当滚动切分特性开启时有效。默认为0，表示不备份，切分则删除
  rotateBackupCompress:  0             # 滚动切分文件的压缩比（0-9）。默认为0，表示不压缩
  rotateCheckInterval:   "1h"          # 滚动切分的时间检测间隔，一般不需要设置。默认为1小时
  stdoutColorDisabled:   false         # 关闭终端的颜色打印。默认开启
  writerColorEnable:     false         # 日志文件是否带上颜色。默认false，表示不带颜色
```

多个Logger的配置示例：
```golang
logger:
  path:    "/var/log"
  level:   "all"
  stdout:  false
  logger1:
    path:    "/var/log/logger1"
    level:   "dev"
    stdout:  false
  logger2:
    path:    "/var/log/logger2"
    level:   "prod"
    stdout:  true
#我本地如下：
server:
  address:     ":8002"
#  openapiPath: "/api.json"
#  swaggerPath: "/swagger"

logger:
  path: "./log/"
  file: "{Y-m-d}.log"
  level : "all"
  header: true
  stdout: false
  info:
    path: "./log/user/info/"
    file: "{Y-m-d}.log"
    level: "INFO"
    stdout: false
  error:
    path: "./log/user/error/"
    file: "{Y-m-d}.log"
    level: "ERRO"
    stdout: false
```
写入区分的目录类别日志：
如下
```golang
g.Log("info").Info(ctx, g.Map{"uid": 1011110, "name": "111john"})

type User struct {
	Uid  int    `json:"uid"`
	Name string `json:"name"`
}

g.Log("error").Error(ctx, User{100, "john"})
```

组件通用Handler
组件提供了一些常用的日志Handler，方便开发者使用，提高开发效率。

HandlerJson
该Handler可以```将日志内容转换为Json格式```打印，方法名：
```glog.SetDefaultHandler(glog.HandlerJson)```
```golang
package main

import (
	"context"

	"github.com/gogf/gf/v2/frame/g"
	"github.com/gogf/gf/v2/os/glog"
)

func main() {
	ctx := context.TODO()
	#生成以json格式的日志
	glog.SetDefaultHandler(glog.HandlerJson)

	g.Log().Debug(ctx, "Debugging...")
	glog.Warning(ctx, "It is warning info")
	glog.Error(ctx, "Error occurs, please have a check")
}
```
没有加载context的时候可以，用ctx := context.TODO()
```shell
ctx := context.TODO()
```

### 自定义中间件
修改文件地址：internal/logic/middleware/middleware.go
#### 添加日志中间件：
（前置中间件）
```golang
func (s *sMiddleware) MiddlewareLog(r *ghttp.Request) {
	//设置日志格式为json
	glog.SetDefaultHandler(glog.HandlerJson)
	rawQuery := r.URL.RawQuery
	host := r.URL.Host
	path := r.URL.Path
	method := r.Method
	clientIp := r.GetClientIp()
	//记录所有请求
	ctx := context.TODO()
	g.Log().Info(ctx, g.Map{"host": host, "path": path, "method": method, "rawQuery": rawQuery, "clientIp": clientIp})

	//前置中间件
	r.Middleware.Next()

}
```
#### 添加报错处理中间件：
(后置中间件)
```golang
func (s *sMiddleware) MiddlewareErrorHandler(r *ghttp.Request) {
	r.Middleware.Next()
	if r.Response.Status >= http.StatusInternalServerError {
		r.Response.ClearBuffer()
		r.Response.WriteJson(ghttp.DefaultHandlerResponse{
			Code:    r.Response.Status, //请求成功  想改成200自己来
			Message: "服务器开小差了，请稍后再试",
			Data:    "",
		})
	}
}
```
#### 添加返回json格式中间件：
（后置中间件）
```golang
func (s *sMiddleware) MiddlewareResponseEcf(r *ghttp.Request) {
	r.Middleware.Next()

	//后置中间件
	if r.Response.BufferLength() > 0 {
		return
	}
	//定义接受的相应结果及错误
	var (
		msg string
		err = r.GetError()
		res = r.GetHandlerResponse()
		//s=r.params
		//code = gerror.Code(err)
	)

	//返回相应对象  及 获取不了的错误结果
	if err != nil {
		code := gerror.Code(err) // 还没理解  后续补充
		if code == gcode.CodeNil {
			code = gcode.CodeInternalError
		}
		r.Response.WriteJson(v1.DefaultHandlerResponse{
			Code: http.StatusInternalServerError, //服务器内部错误  想改成500自己来
			Msg:  code.Message(),
			Data: nil,
		})
		return
	}

	if ecfResponse, ok := res.(*v1.EcfRes); ok {
		//没有问题返回结果
		r.Response.WriteJson(v1.DefaultHandlerResponse{
			Code: ecfResponse.Code, //请求成功  想改成200自己来
			Msg:  ecfResponse.Msg,
			Data: ecfResponse.Data,
		})
	} else {
		msg = "成功"
		//没有问题返回结果
		r.Response.WriteJson(v1.DefaultHandlerResponse{
			Code: http.StatusOK, //请求成功  想改成200自己来
			Msg:  msg,
			Data: res,
		})
	}

}
```

+ 注意获取接口类型中的结构体字段的值，使用```断言```
```shell
if ecfResponse, ok := res.(*v1.EcfRes); ok {
		//没有问题返回结果
		r.Response.WriteJson(v1.DefaultHandlerResponse{
			Code: ecfResponse.Code, //请求成功  想改成200自己来
			Msg:  ecfResponse.Msg,
			Data: ecfResponse.Data,
		})
	}
```

### gtoken使用
一、下载gtoken
```shell
go get github.com/goflyfox/gtoken
```
二、下载完包后整理依赖文件
```shell
go mod tidy
```
三、在中间件包中添加login登录方法
```golang
func AuthLogin(r *ghttp.Request) (string, interface{}) {
	username := r.GetPostString("username")
	passwd := r.GetPostString("passwd")

	// TODO 进行登录校验
	if username == "" || passwd == "" {
		r.Response.WriteJson(gtoken.Fail("账号或密码错误."))
		r.ExitAll()
	}

	return username, ""
}
```
四、启动gtoken
```golang
loginFunc := AuthLogin
gfToken := &gtoken.GfToken{
	LoginPath:       "/auth-login", //上面的方法地址,对应的是/api/auth-login
	LoginBeforeFunc: loginFunc, //只要固定类型方法：(r *ghttp.Request) (string, interface{})
	LogoutPath:      "/api/logout", //暂时可以不写
}
```
五、postman请求接口，获取token
+ 请求地址：/api/auth-login
+ 输入账号/密码：
+ 返回值如下：
```shell
{
    "code": 0,
    "msg": "success",
    "data": {
        "token": "F1om0nykaRN7gBi3u+Nr5RYYQSVCfkXjDGVz9lLaT+mnIoss6/knJ1uBT19A6QBW"
    }
}
```
六、附带上面生成的token请求需鉴权接口
+ 如请求地址：/api/info
+ 选择Bearer Token，输入：F1om0nykaRN7gBi3u+Nr5RYYQSVCfkXjDGVz9lLaT+mnIoss6/knJ1uBT19A6QBW
+ 返回如下：
```shell
{
    "code": 200,
    "msg": "成功",
    "data": {
        "name": "jarvenwang"
    }
}
```

