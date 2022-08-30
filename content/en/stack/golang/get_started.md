---
author: "wangjinbao"
title: "golang安装、配置及版本升级"
date: 2022-06-07T12:00:06+09:00
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
  -
image: images/feature3/go.png
---


## 一、下载
去官网下载 ，国内地址：[https://golang.google.cn/dl/](https://golang.google.cn/dl/) 
选择对应的版本即可，我本地使用的是[https://golang.google.cn/dl/go1.18.3.darwin-arm64.pkg](https://golang.google.cn/dl/go1.18.3.darwin-arm64.pkg)

## 二、安装及设置环境变量
修改文件：
/etc/profile

```shell
$ sudo vim /etc/profile
# 在文件尾部加上：
#golang
export GOROOT=/usr/local/go
export GOPATH=/Users/wangdante/go
export GOBIN=$GOROOT/bin
export PATH=$PATH:$GOBIN
# 使生效
$ source /etc/profile
```
我使用zsh，所以也修改~/.zshrc
```shell
vim ~/.zshrc
#golang
export GOROOT=/usr/local/go
export GOPATH=/Users/wangdante/go
export GOBIN=$GOROOT/bin
export PATH=$PATH:$GOBIN
# 使生效
source ~/.zshrc
```
打开新terminal
```shell
go env
# 即可查看到GOPATH生效了
GO111MODULE="on"
GOARCH="amd64"
GOBIN="/usr/local/go/bin"
GOCACHE="/Users/wangdante/Library/Caches/go-build"
GOENV="/Users/wangdante/Library/Application Support/go/env"
GOEXE=""
GOEXPERIMENT=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GOINSECURE=""
GOMODCACHE="/Users/wangdante/go/pkg/mod"
GONOPROXY=""
GONOSUMDB=""
GOOS="darwin"
GOPATH="/Users/wangdante/go"
GOPRIVATE=""
GOPROXY="https://goproxy.cn,direct"
GOROOT="/usr/local/go"
GOSUMDB="sum.golang.org"
GOTMPDIR=""
GOTOOLDIR="/usr/local/go/pkg/tool/darwin_amd64"
GOVCS=""
GOVERSION="go1.18.5"
GCCGO="gccgo"
GOAMD64="v1"
AR="ar"
CC="clang"
CXX="clang++"
CGO_ENABLED="1"
GOMOD="/dev/null"
GOWORK=""
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -arch x86_64 -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=/var/folders/9s/kn4nm_092wq6vp1crgf6q7th0000gn/T/go-build1296574441=/tmp/go-build -gno-record-gcc-switches -fno-common"
```

## 三、新建三个文件夹
```shell
mkdir -p $GOPATH/{bin,src,pkg}

```
## 四、运行第一个go程序
```shell
cd /Users/wangdante/go/src/
# (你的项目目录名称随便取)
mkdir wjb
cd wjb
$ pwd
/Users/wangdante/go/src/wjb
#新建一个文件：
vim test.go
# 内容：
package main

import "fmt"

func main() {
   fmt.Println("Hello, World!")
}

#保存并运行，方式两种
#方式一
$ go run test.go                                                                       1 ↵
Hello, World!

#方式二 进入项目目录：
sudo go install
#PS：报错
#go: go.mod file not found in current directory or any parent directory; see 'go help modules'
#解决方法：与 golang 的包管理有关。
#go env -w GO111MODULE=auto

之后在GOPATH下面的bin目录生成一个hello的可执行文件
sh hello
(#go build test.go)
```
## 五、go环境的卸载
#### 步骤一：
删除/usr/local下的go目录<font color="red">(备注: 这个目录是安装go的时候自动生成的. 如果删除完, 使用 go version, 会报找不到go命令)</font>

#### 步骤二：
删除Path环境变量 <font color="red">（备注: 这里,我只是想换一个版本, 所以, goPath还是需要的,所以不用删除）</font>

#### 步骤三：
删除配置文件信息: 在 <font color="red">/etc/profile </font> 或者 <font color="red">$HOME/.profile</font> 或者 <font color="red">$HOME/.bahs_profile</font> 或 <font color="red">~/.zshrc</font> 中删除bin的设置

#### 步骤四：
删除mac os x的安装包安装的文件, 删除 <font color="red">/etc/paths.d/go</font> 文件
(我只用了第一步, 重新安装, 其他都还继续使用)

## 六、升级go版本
<font color="red">(升级前先卸载之前的golang版本)</font>
```shell
#之前
$ go version
go version go1.17.11 darwin/arm64
#之后
$ go version                                                                            127 ↵
go version go1.18.5 darwin/amd64
```
#### 步骤一、官网下载版本1.18.5
下载地址：[https://golang.google.cn/dl/](https://golang.google.cn/dl/)
![/images/docImages/img.png](/images/docImages/img.png)

#### 步骤二、删除/usr/local/go
删除/usr/local下的go目录<font color='#aed581'>(备注: 这个目录是安装go的时候自动生成的. 如果删除完, 使用 go version, 会报找不到go命令)</font>

#### 步骤三、双击amd安装包
双击 go1.18.5.darwin-arm64.pkg 安装包，一路下一步
之后go version验证
