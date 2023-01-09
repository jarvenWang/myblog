---
author: "wangjinbao"
title: "linux安装golang"
date: 2021-01-02 01:01:00
description: "整理在mac上的golang安装、配置及版本升级"
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


## 一、下载
去官网下载 ，国内地址：[https://golang.google.cn/dl/](https://golang.google.cn/dl/)
![/images/docImages/img_2.png](/images/docImages/img_2.png)
```shell
$ wget https://golang.google.cn/dl/go1.16.7.linux-amd64.tar.gz
```

## 二、解压(需要root权限)
```shell
$ sudo tar -C /usr/local -xzf go1.16.7.linux-amd64.tar.gz
```

## 三、项目目录
```shell
$ mkdir -p /home/go/src /home/go/pkg /home/go/bin
```
## 四、环境变量
```shell
vi /etc/profile
shift + g 可以跳转到最后一行 然后按a,回车到新的一行

export GOROOT=/usr/local/go
export GOPATH=/home/gopath
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin

然后按ESC,输入 :wq
source /etc/profile

```
## 五、校验环境

```shell
$ go env
```

## 六、测试程序
```shell
$ cd /home/go/src
$ vi main.go
```
```shell
package main
import "fmt"
 
func main() {
　　fmt.Println("Hello, World!")
}
```
然后按ESC,输入 :wq
```shell
go run ./main.go
```

