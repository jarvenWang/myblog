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
### GetArg*参数获取
+ GetArg方法用以获取默认解析的命令行参数，参数通过输入索引位置获取，索引位置从0开始，但往往我们需要获取的参数是从1开始，因为索引0的参数是程序名称。
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
+ GetArgAll方法用于获取所有的命令行参数。
```go
func ExampleGetArgAll() {
    gcmd.Init("gf", "build", "main.go", "-o=gf.exe", "-y")
    fmt.Printf(`%#v`, gcmd.GetArgAll())

	// Output:
	// []string{"gf", "build", "main.go"}
}
```
### GetOpt*选项获取
+ GetOpt方法用以获取默认解析的命令行选项，选项通过名称获取，并且选项的输入没有顺序性，可以输入到任意的命令行位置。当给定名称的选项数据不存在时，返回nil。注意判断不带数据的选项是否存在时，可以通过GetOpt(name) != nil方式。
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
+ GetOptAll方法用于获取所有的选项。
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
+ 数据集合
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
+ 数据记录
```go
r, err := g.Model("order").Where("status", 1).One()
if err != nil {
    return err
}
if len(r) == 0 {
    // 结果为空
}
```
也可以使用IsEmpty方法：
```go
r, err := g.Model("order").Where("status", 1).One()
if err != nil {
    return err
}
if r.IsEmpty() {
    // 结果为空
}
```
+ 数据字段值
返回的是一个"泛型"变量，这个只能使用IsEmpty来判断是否为空了。
```go
r, err := g.Model("order").Where("status", 1).Value()
if err != nil {
	return err
}
if r.IsEmpty() {
    // 结果为空
}
```
+ 字段值数组
  查询返回字段值数组本身类型为[]gdb.Value类型，因此直接判断长度是否为0即可。
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

### ORM时区处理
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
这里由于通过time.Parse创建的time.Time时间对象是UTC时区，那么提交到数据库执行时将会被底层的driver修改为+8时区。

```go
t1, _ := time.ParseInLocation("2006-01-02 15:04:05", "2020-10-27 10:00:00", time.Local)
t2, _ := time.ParseInLocation("2006-01-02 15:04:05", "2020-10-27 11:00:00", time.Local)
db.Model("user").Ctx(ctx).Where("create_time>? and create_time<?", t1, t2).One()
// SELECT * FROM `user` WHERE create_time>'2020-10-27 10:00:00' AND create_time<'2020-10-27 11:00:00'
```
这里由于通过time.ParseInLocation创建的time.Time时间对象是+8时区，和loc=Local的时区一致，那么提交到数据库执行时不会被底层的driver修改。







