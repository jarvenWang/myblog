---
author: "wangjinbao"
title: "goframe框架集成任务队列machinery和定时任务"
date: 2021-12-01T12:00:06+09:00
description: "goframe框架集成分布式异步任务队列machinery"
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


## Machinery
Golang的分布式任务队列还不算多，目前比较成熟的应该就只有 Machinery 了。
如果熟悉Python中的异步任务框架的话，想必一定听过Celery。
异步任务框架是什么呢？异步任务的主要作用是将需要长时间执行 的代码放到一个单独的程序中，例如调用第三方邮件接口，但是这个接口可能非常慢才响应，而你又想确保自己的API及时响应。这个 时候就可以采用异步任务来进行解耦。

### 异步任务的组成和流程
一般来说，异步任务都由这么几部分组成：

```shell
- broker：用来传递信息的，想象成“信使”，作用是暂时保存产生的任务以便于消费
- 生产者：它负责产生任务
- 消费者：它负责消费任务
- result backend：这个不是必需，但是如果有保存结果的需要，那么就需要它。
```

一般来流程：

```shell
生产者发布任务 -> broker -> 消费者竞争任务，然后消费 -> 
(可选：消费后向broker确认已经消费，然后broker删除此任务，否则将超时重发任务) 
-> result backend保存结果
```
### 集成Machinery到goframe框架
#### 步骤一
首先我们来把 Machinery 代码拉下来：
```shell
$ go get github.com/RichardKnop/machinery/v2
```
在internal/cmd/cmd.go文件：
```golang
var (
	Main = gcmd.Command{
		Func: func(ctx context.Context, parser *gcmd.Parser) (err error) {
		    //获取命令参数并解析 
		    //** goland中设置debug加参数(-server worker)
		    //** 1、添加go build 执行脚本 
		    //** 2、设置 Working directory:/Users/wangdante/D/kugou/go/src/tme_doc_api 
		    //** 3、Program arguments:-server worker)
		    
			//-server worker
			opt := gconv.Map(parser.GetOptAll())
			
			server := opt["server"]
			if server == "worker" {
				service.Machinery().Worker(ctx)
				return nil
			} else {
				s := g.Server()
				//------不需要鉴权------
				s.Group("/api", func(group *ghttp.RouterGroup) {
					group.Middleware(
						ghttp.MiddlewareCORS,
						service.Middleware().MiddlewareLog,
						service.Middleware().MiddlewareErrorHandler,
						service.Middleware().MiddlewareResponseEcf,
					)
					//绑定路由
					group.Bind(
						controller.Hello,
						controller.Public,
						//controller.User,
					)
				})
				//------需要鉴权------
				s.Group("/api", func(group *ghttp.RouterGroup) {
					group.Middleware(
						ghttp.MiddlewareCORS,
						service.Middleware().MiddlewareLog,
						service.Middleware().MiddlewareErrorHandler,
						service.Middleware().MiddlewareResponseEcf,
						service.Middleware().MiddlewareToken,
					)
					//gfToken.Middleware(ctx, group)
					//绑定路由
					group.Bind(
						controller.User,
					)
				})

				s.Run()
				return nil
			}

		},
	}
)
```
#### 步骤二
新建文件logic/machinery/machinery.go
同时添加 jaeger.go 、 server.go 、 worker.go (来至拉下来的代码)
在machinery文件中添加三个方法：
```golang
func (s *sMachinery) Worker(ctx context.Context) error {
	consumerTag := "machinery_worker_ecf"

	cleanup, err := SetupTracer(consumerTag)
	if err != nil {
		log.FATAL.Fatalln("Unable to instantiate a tracer:", err)
	}
	defer cleanup()

	server, err := service.Machinery().StartServer()
	if err != nil {
		return err
	}
	// The second argument is a consumer tag
	// Ideally, each worker should have a unique tag (worker1, worker2 etc)
	worker := server.NewWorker(consumerTag, 0)

	// Here we inject some custom code for error handling,
	// start and end of task hooks, useful for metrics for example.
	errorHandler := func(err error) {
		log.ERROR.Println("I am an error handler:", err)
	}

	preTaskHandler := func(signature *tasks.Signature) {
		log.INFO.Println("I am a start of task handler for:", signature.Name)
	}

	postTaskHandler := func(signature *tasks.Signature) {
		log.INFO.Println("I am an end of task handler for:", signature.Name)
	}

	worker.SetPostTaskHandler(postTaskHandler)
	worker.SetErrorHandler(errorHandler)
	worker.SetPreTaskHandler(preTaskHandler)

	return worker.Launch()
}

func (s *sMachinery) Send(ctx context.Context) error {
	cleanup, err := SetupTracer("sender")
	if err != nil {
		log.FATAL.Fatalln("Unable to instantiate a tracer:", err)
	}
	defer cleanup()

	server, err := service.Machinery().StartServer()
	if err != nil {
		return err
	}

	var (
		addTask0 tasks.Signature
		//addTask1, addTask2                                tasks.Signature
		//multiplyTask0, multiplyTask1                      tasks.Signature
		//sumIntsTask, sumFloatsTask, concatTask, splitTask tasks.Signature
		//panicTask                                         tasks.Signature
		//longRunningTask                                   tasks.Signature
	)

	var initTasks = func() {
		addTask0 = tasks.Signature{
			Name: "add",
			Args: []tasks.Arg{
				{
					Type:  "int64",
					Value: 1,
				},
				{
					Type:  "int64",
					Value: 1,
				},
			},
		}

	}

	/*
	 * Lets start a span representing this run of the `send` command and
	 * set a batch id as baggage so it can travel all the way into
	 * the worker functions.
	 */
	span, ctx := opentracing.StartSpanFromContext(context.Background(), "send")
	defer span.Finish()

	batchID := uuid.New().String()
	span.SetBaggageItem("batch.id", batchID)
	span.LogFields(opentracinglog.String("batch.id", batchID))

	log.INFO.Println("Starting batch:", batchID)
	/*
	 * First, let's try sending a single task
	 */
	initTasks()

	log.INFO.Println("Single task:")

	asyncResult, err := server.SendTaskWithContext(ctx, &addTask0)
	if err != nil {
		return fmt.Errorf("Could not send task: %s", err.Error())
	}

	results, err := asyncResult.Get(time.Millisecond * 5)
	if err != nil {
		return fmt.Errorf("Getting task result failed with error: %s", err.Error())
	}
	log.INFO.Printf("1 + 1 = %v\n", tasks.HumanReadableResults(results))

	return nil
}

func (s *sMachinery) StartServer() (*machinery.Server, error) {

	//=====创建并配置broker=====

	//读取redis配置参数
	var ctx = gctx.New()
	redisData, _ := g.Cfg().Get(ctx, "redis")
	deMap := redisData.MapDeep()["default"]
	var redisAddress, redisPass, redisQueue string
	var redisDB, redisExpire int
	if cnf, ok := deMap.(map[string]interface{}); ok {
		redisAddress = common.Strval(cnf["address"])
		redisPass = common.Strval(cnf["pass"])
		redisDB, _ = strconv.Atoi(common.Strval(cnf["work"]))
		redisQueue = common.Strval(cnf["default_queue"])
		redisExpire, _ = strconv.Atoi(common.Strval(cnf["expire_in"]))
	}

	cnf := &config.Config{
		DefaultQueue:    redisQueue,
		ResultsExpireIn: redisExpire,
		Redis: &config.RedisConfig{
			MaxIdle:                3,
			IdleTimeout:            240,
			ReadTimeout:            15,
			WriteTimeout:           15,
			ConnectTimeout:         15,
			NormalTasksPollPeriod:  1000,
			DelayedTasksPollPeriod: 500,
		},
	}

	// Create server instance
	broker := redisbroker.New(cnf, redisAddress, redisPass, "", redisDB)
	backend := redisbackend.New(cnf, redisAddress, redisPass, "", redisDB)
	lock := eagerlock.New()
	server := machinery.NewServer(cnf, broker, backend, lock)

	//=====创建任务tasks=====

	// Register tasks
	tasksMap := map[string]interface{}{
		"add": exampletasks.Add,
		// "multiply":          exampletasks.Multiply,
		//"sum_ints":          exampletasks.SumInts,
		// "sum_floats":        exampletasks.SumFloats,
		// "concat":            exampletasks.Concat,
		// "split":             exampletasks.Split,
		// "panic_task":        exampletasks.PanicTask,
		// "long_running_task": exampletasks.LongRunningTask,
	}

	//=====注册任务tasks=====

	return server, server.RegisterTasks(tasksMap)
}
```

## Machinery定时任务
### 步骤一：创建并配置broker
体能在方法StartServer中
```go
//=====创建并配置broker=====

//读取redis配置参数
var ctx = gctx.New()
redisData, _ := g.Cfg().Get(ctx, "redis")
deMap := redisData.MapDeep()["default"]
var redisAddress, redisPass, redisQueue string
var redisDB, redisExpire int
if cnf, ok := deMap.(map[string]interface{}); ok {
	redisAddress = common.Strval(cnf["address"])
	redisPass = common.Strval(cnf["pass"])
	redisDB, _ = strconv.Atoi(common.Strval(cnf["work"]))
	redisQueue = common.Strval(cnf["default_queue"])
	redisExpire, _ = strconv.Atoi(common.Strval(cnf["expire_in"]))
}

cnf := &config.Config{
	DefaultQueue:    redisQueue,
	ResultsExpireIn: redisExpire,
	Redis: &config.RedisConfig{
		MaxIdle:                3,
		IdleTimeout:            240,
		ReadTimeout:            15,
		WriteTimeout:           15,
		ConnectTimeout:         15,
		NormalTasksPollPeriod:  1000,
		DelayedTasksPollPeriod: 500,
	},
}
```
### 步骤二：创建server实例
```go
// Create server instance
broker := redisbroker.New(cnf, redisAddress, redisPass, "", redisDB)
backend := redisbackend.New(cnf, redisAddress, redisPass, "", redisDB)
lock := eagerlock.New()
server := machinery.NewServer(cnf, broker, backend, lock)
```
### 步骤三：注册普通任务tasks
```go
//=====创建任务tasks=====

// Register tasks
tasksMap := map[string]interface{}{
	"add":                exampletasks.Add,
	"transferDepartment": s.TransferDepartment,
	"transferBU":         s.TransferBU,
	"transferStaff":      s.TransferStaff,
	"ecfDepartment":      s.EcfDepartment,
	"ecfBU":              s.EcfBU,
	"ecfStaff":           s.EcfStaff,
}

return server, server.RegisterTasks(tasksMap)
```
### 步骤四：注册定时任务tasks
在注册完普通任务return注册定时任务即可
公式：Cron * * * * ?: `minute`, `hour`, `day of month`, `month and day of week`
```go
signatureBU := &tasks.Signature{
	Name: "transferBU",
}
server.RegisterPeriodicTask("57 9 * * ?", "periodic-task-bu", signatureBU)
```

### 步骤五：写好注册定时任务执行方法
```go
// TransferDepartment
/**
 * @Name: TransferDepartment
 * @Description: 集团数据中转-行政组织
 * @receiver s
 * @param args
 * @return int64
 * @return error
 */
func (s *sMachinery) TransferDepartment(args ...int64) (int64, error) {
	ctx := context.TODO()
	service.Tme().GetCenterData(ctx, "department")

	return 1, nil
}

// TransferBU
/**
 * @Name: TransferBU
 * @Description: 集团数据中转-业务单元
 * @receiver s
 * @param args
 * @return int64
 * @return error
 */
func (s *sMachinery) TransferBU(args ...int64) (int64, error) {
	ctx := context.TODO()
	service.Tme().GetCenterData(ctx, "business_unit")

	return 1, nil
}
```
