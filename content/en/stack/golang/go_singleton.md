---
author: "wangjinbao"
title: "Golang的单例模式"
date: 2021-01-01 00:00:07
description: "单例模式也叫单子模式,在它的核心结构中只包含一个被称为单例的特殊类，能够保证系统运行中一个类只创建一个实例"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags: 
- golang
categories:
- golang



---
对于设计模式，更多的则是仁者见仁智者见智，要在实际工作中不断的积累，再进行深度的思考，才能逐渐形成的一种思维。

`单例模式` 也叫单子模式，是常用的模式之一，在它的核心结构中只包含一个被称为单例的特殊类，能够保证系统运行中一个类只创建一个实例，本节我们就来介绍一下Go语言中的单例模式。

## 单例模式实现
Go语言实现单例模式的有四种方式，分别是 <font color='cyan'>**懒汉式**</font> 、 <font color='cyan'>**饿汉式**</font> 、 <font color='cyan'>**双重检查**</font> 和 <font color='cyan'>**sync.Onc**</font>。
不管那种模式最终目的只有一个，就是只实例化一次，只允许一个实例存在
### 1) 懒汉式——非线程安全
<font color='cyan'>**懒汉式**</font> :创建对象时比较懒，先不急着创建对象，在需要加载配置文件的时候再去创建
非线程安全，指的是在多线程下可能会创建多次对象。
```go
//使用结构体代替类
type Tool struct {
    values int
}

//建立私有变量
var instance *Tool

//获取单例对象的方法，引用传递返回
func GetInstance() *Tool {
    if instance == nil {
        instance = new(Tool)
    }
    return instance
}
```
在非线程安全的基本上，利用 Sync.Mutex 进行加锁保证线程安全，但由于每次调用该方法都进行了加锁操作，在性能上不是很高效
```go
//锁对象
var lock sync.Mutex

//加锁保证线程安全
func GetInstance() *Tool {
    lock.Lock()
    defer lock.Unlock()
    if instance == nil {
        instance = new(Tool)
    }

    return instance
}
```

### 2) 饿汉式
直接创建好对象，不需要判断为空，同时也是线程安全，唯一的缺点是在导入包的同时会创建该对象，并持续占有在内存中。

Go语言饿汉式可以使用 <font color='cyan'>**init 函数**</font>，也可以使用 <font color='cyan'>**全局变量**</font>。

init 函数:
```go
type cfg struct {
}
var cfg *config
func init()  {
   cfg = new(config)
}
// NewConfig 提供获取实例的方法
func NewConfig() *config {
   return cfg
}
type config struct {  
}
```
全局变量:
```go
type config struct {  
}
//全局变量
var cfg *config = new(config)
// NewConfig 提供获取实例的方法
func NewConfig() *config {
   return cfg
}
```

### 3) 双重检查
在懒汉式（线程安全）的基础上再进行优化，减少加锁的操作，保证线程安全的同时不影响性能
```go
//锁对象
var lock sync.Mutex

//第一次判断不加锁，第二次加锁保证线程安全，一旦对象建立后，获取对象就不用加锁了。
func GetInstance() *Tool {
    if instance == nil {
        lock.Lock()
        if instance == nil {
            instance = new(Tool)
        }
        lock.Unlock()
    }
    return instance
}
```

### 4) sync.Once
通过 sync.Once 来确保创建对象的方法只执行一次
```go
var once sync.Once

func GetInstance() *Tool {
    once.Do(func() {
        instance = new(Tool)

    })
    return instance
}
```
sync.Once 内部本质上也是双重检查的方式，但在写法上会比自己写双重检查更简洁，以下是 Once 的源码
```go
func (o *Once) Do(f func()) {
　　//判断是否执行过该方法，如果执行过则不执行
    if atomic.LoadUint32(&o.done) == 1 {
        return
    }
    // Slow-path.
    o.m.Lock()
    defer o.m.Unlock()
　　//进行加锁，再做一次判断，如果没有执行，则进行标志已经扫行并调用该方法
    if o.done == 0 {
        defer atomic.StoreUint32(&o.done, 1)
        f()
    }
}
```