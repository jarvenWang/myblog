---
author: "wangjinbao"
title: "goframe框架-2"
date: 2021-02-03 01:01:00
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

## 命令管理

### GetArg\*参数获取

- GetArg 方法用以获取默认解析的命令行参数，参数通过输入索引位置获取，索引位置从 0 开始，但往往我们需要获取的参数是从 1 开始，因为索引 0 的参数是程序名称。

```go
func ExampleGetArg() {
	gcmd.Init("gf", "build", "main.go", "-o=gf.exe", "-y")
	fmt.Printf(
		`Arg[0]: "%v", Arg[1]: "%v", Arg[2]: "%v", Arg[3]: "%v"`,
		gcmd.GetArg(0), gcmd.GetArg(1), gcmd.GetArg(2), gcmd.GetArg(3),
	)

	// Output:
	// Arg[0]: "gf", Arg[1]: "build", Arg[2]: "main.go", Arg[3]: ""
}
```

- GetArgAll 方法用于获取所有的命令行参数。

```go
func ExampleGetArgAll() {
    gcmd.Init("gf", "build", "main.go", "-o=gf.exe", "-y")
    fmt.Printf(`%#v`, gcmd.GetArgAll())

	// Output:
	// []string{"gf", "build", "main.go"}
}
```

### GetOpt\*选项获取

- GetOpt 方法用以获取默认解析的命令行选项，选项通过名称获取，并且选项的输入没有顺序性，可以输入到任意的命令行位置。当给定名称的选项数据不存在时，返回 nil。注意判断不带数据的选项是否存在时，可以通过 GetOpt(name) != nil 方式。

```go
func ExampleGetOpt() {
	gcmd.Init("gf", "build", "main.go", "-o=gf.exe", "-y")
	fmt.Printf(
		`Opt["o"]: "%v", Opt["y"]: "%v", Opt["d"]: "%v"`,
		gcmd.GetOpt("o"), gcmd.GetOpt("y"), gcmd.GetOpt("d", "default value"),
	)

	// Output:
	// Opt["o"]: "gf.exe", Opt["y"]: "", Opt["d"]: "default value"
}
```

- GetOptAll 方法用于获取所有的选项。

```go
func ExampleGetOptAll() {
	gcmd.Init("gf", "build", "main.go", "-o=gf.exe", "-y")
	fmt.Printf(`%#v`, gcmd.GetOptAll())

	// May Output:
	// map[string]string{"o":"gf.exe", "y":""}
}
```

获取 命令后追加的选项参数，如 <font color="red">go build ecf -server worker</font>

```go
//goframe框架中
opt := gconv.Map(parser.GetOptAll())

server := opt["server"]
if server == "worker" {
	service.Machinery().Worker(ctx)
	return nil
} else {
	s := g.Server()
	//------不需要鉴权------
	s.Group("/api", func(group *ghttp.RouterGroup) {
	...
	})
	//------需要鉴权------
	s.Group("/api", func(group *ghttp.RouterGroup) {
    ...
	})

	s.Run()
	return nil
}
```

## 数据库结果处理

### 查询结果列表中添加字段

```go
res, _ := model.All()
gdbRes, ok := res.(gdb.Result)
resList := gdbRes.List()
if ok {
	for i, _ := range resList {
		//通过BU获取说明
		setid_desc := service.Common().GetBUDescByBU(resList[i]["setid"])
		resList[i]["setid_desc"] = setid_desc
        ...
	}
}
```

### 为空判断

- 数据集合

```go
r, err := g.Model("order").Where("status", 1).All()
if err != nil {
	return err
}
if len(r) == 0 {
    // 结果为空
}

```

也可以使用 <font color='red'>IsEmpty</font> 方法：

```go
r, err := g.Model("order").Where("status", 1).All()
if err != nil {
	return err
}
if r.IsEmpty() {
    // 结果为空
}
```

- 数据记录

```go
r, err := g.Model("order").Where("status", 1).One()
if err != nil {
    return err
}
if len(r) == 0 {
    // 结果为空
}
```

也可以使用 IsEmpty 方法：

```go
r, err := g.Model("order").Where("status", 1).One()
if err != nil {
    return err
}
if r.IsEmpty() {
    // 结果为空
}
```

- 数据字段值
  返回的是一个"泛型"变量，这个只能使用 IsEmpty 来判断是否为空了。

```go
r, err := g.Model("order").Where("status", 1).Value()
if err != nil {
	return err
}
if r.IsEmpty() {
    // 结果为空
}
```

- 字段值数组
  查询返回字段值数组本身类型为[]gdb.Value 类型，因此直接判断长度是否为 0 即可。

```go
// Array/FindArray
r, err := g.Model("order").Fields("id").Where("status", 1).Array()
if err != nil {
    return err
}
if len(r) == 0 {
    // 结果为空
}
```

### ORM 时区处理

设置`loc=Local`
配置文件：

```go
database:
  link: "mysql:root:12345678@tcp(127.0.0.1:3306)/test?loc=Local"
```

示例代码：

```go
t1, _ := time.Parse("2006-01-02 15:04:05", "2020-10-27 10:00:00")
t2, _ := time.Parse("2006-01-02 15:04:05", "2020-10-27 11:00:00")
db.Model("user").Ctx(ctx).Where("create_time>? and create_time<?", t1, t2).One()
// SELECT * FROM `user` WHERE create_time>'2020-10-27 18:00:00' AND create_time<'2020-10-27 19:00:00'
```

这里由于通过 time.Parse 创建的 time.Time 时间对象是 UTC 时区，那么提交到数据库执行时将会被底层的 driver 修改为+8 时区。

```go
t1, _ := time.ParseInLocation("2006-01-02 15:04:05", "2020-10-27 10:00:00", time.Local)
t2, _ := time.ParseInLocation("2006-01-02 15:04:05", "2020-10-27 11:00:00", time.Local)
db.Model("user").Ctx(ctx).Where("create_time>? and create_time<?", t1, t2).One()
// SELECT * FROM `user` WHERE create_time>'2020-10-27 10:00:00' AND create_time<'2020-10-27 11:00:00'
```

这里由于通过 time.ParseInLocation 创建的 time.Time 时间对象是+8 时区，和 loc=Local 的时区一致，那么提交到数据库执行时不会被底层的 driver 修改。

## gen dao

`gf gen dao`用于生成 model 数据结构定义文件
`gen model未来不再推荐这种方式，而是推荐使用 dao 的使用方式`
goframe 中的 自动生成的 model 在`dao`文件夹下的`internal`文件夹下。
不可以修改，`Code generated by GoFrame CLI tool. DO NOT EDIT.`
所以自定义的 model 层在`internal`同级目录的.go 文件中编写

```golang
package dao

import (
	"ecf/internal/dao/internal"
	"fmt"
)

// internalUploadFileDao is internal type for wrapping internal DAO implements.
type internalUploadFileDao = *internal.UploadFileDao

// uploadFileDao is the data access object for table ecf_upload_file.
// You can define custom methods on it to extend its functionality as you wish.
type uploadFileDao struct {
	internalUploadFileDao
}

var (
	// UploadFile is globally public accessible object for table ecf_upload_file operations.
	// UploadFile是全局公式的访问数据表对象
	UploadFile = uploadFileDao{
		internal.NewUploadFileDao(),
	}
)

// Fill with you ideas below.

//todo
//type SettingGetInput struct {
//	Key string
//}
func A() bool {
	ab := &internal.UploadFileDao{}
	UploadId := ab.Columns().UploadId
	fmt.Println(UploadId)
	// UploadFile是全局公式的访问数据表对象
	// 获取指定字段
	c := UploadFile.Columns().UploadId
	fmt.Println(c)
	return false
}

```
