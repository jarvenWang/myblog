---
author: "wangjinbao"
title: "Golang的常用内置包简介"
date: 2021-01-01 00:00:05
description: "标准的Go语言代码库中包含了大量的包，并且在安装 Go 的时候多数会自动安装到系统中"
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
标准的Go语言代码库中包含了大量的包，并且在安装 Go 的时候多数会自动安装到系统中。我们可以在 $GOROOT/src/pkg 目录中查看这些包。下面简单介绍一些我们开发中常用的包
## 1) fmt
fmt 包实现了格式化的标准输入输出，这与C语言中的 printf 和 scanf 类似。其中的 fmt.Printf() 和 fmt.Println() 是开发者使用最为频繁的函数。

格式化短语派生于C语言，一些短语（%- 序列）是这样使用：

+ %v：默认格式的值。当打印结构时，加号（%+v）会增加字段名；
+ %#v：Go样式的值表达；
+ %T：带有类型的 Go 样式的值表达。

## 2) io
这个包提供了原始的 I/O 操作界面。它主要的任务是对 os 包这样的原始的 I/O 进行封装，增加一些其他相关，使其具有抽象功能用在公共的接口上。

## 3) bufio
bufio 包通过对 io 包的封装，提供了数据缓冲功能，能够一定程度减少大块数据读写带来的开销。

在 bufio 各个组件内部都维护了一个缓冲区，数据读写操作都直接通过缓存区进行。当发起一次读写操作时，会首先尝试从缓冲区获取数据，只有当缓冲区没有数据时，才会从数据源获取数据更新缓冲。

## 4) sort
sort 包提供了用于对切片和用户定义的集合进行排序的功能。

## 5) strconv
strconv 包提供了将字符串转换成基本数据类型，或者从基本数据类型转换为字符串的功能。

## 6) os
os 包提供了不依赖平台的操作系统函数接口，设计像 Unix 风格，但错误处理是 go 风格，当 os 包使用时，如果失败后返回错误类型而不是错误数量。

## 7) sync
sync 包实现多线程中锁机制以及其他同步互斥机制。

## 8) flag
flag 包提供命令行参数的规则定义和传入参数解析的功能。绝大部分的命令行程序都需要用到这个包。

## 9) encoding/json
JSON 目前广泛用做网络程序中的通信格式。`encoding/json` 包提供了对 JSON 的基本支持，比如从一个对象序列化为 JSON 字符串，或者从 JSON 字符串反序列化出一个具体的对象等。

## 10) html/template
主要实现了 web 开发中生成 html 的 template 的一些函数。

## 11) net/http
net/http 包提供 HTTP 相关服务，主要包括 http 请求、响应和 URL 的解析，以及基本的 http 客户端和扩展的 http 服务。

通过 net/http 包，只需要数行代码，即可实现一个爬虫或者一个 Web 服务器，这在传统语言中是无法想象的。

## 12) reflect
reflect 包实现了运行时反射，允许程序通过抽象类型操作对象。通常用于处理静态类型 interface{} 的值，并且通过 Typeof 解析出其动态类型信息，通常会返回一个有接口类型 Type 的对象

## 13) os/exec
os/exec 包提供了执行自定义 linux 命令的相关实现。

## 14) strings
strings 包主要是处理字符串的一些函数集合，包括合并、查找、分割、比较、后缀检查、索引、大小写处理等等。

strings 包与 bytes 包的函数接口功能基本一致。

## 15) bytes
bytes 包提供了对字节切片进行读写操作的一系列函数。字节切片处理的函数比较多，分为基本处理函数、比较函数、后缀检查函数、索引函数、分割函数、大小写处理函数和子切片处理函数等。

## 16) log
log 包主要用于在程序中输出日志。

log 包中提供了三类日志输出接口，Print、Fatal 和 Panic。

+ Print 是普通输出；
+ Fatal 是在执行完 Print 后，执行 os.Exit(1)；
+ Panic 是在执行完 Print 后调用 panic() 方法



