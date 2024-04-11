---
author: "wangjinbao"
title: "Golang包的基本概念"
date: 2021-01-01 00:00:01
description: "包（package）是多个 Go 源码的集合，是一种高级的代码复用方案。Go语言中为我们提供了很多内置包，如 fmt、os、io 等"
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
任何源代码文件必须属于某个包，同时源码文件的第一行有效代码必须是package pacakgeName语句，通过该语句声明自己所在的包

## 包的基本概念
Go语言的包借助了 <font color='cyan'>**目录树**</font> 的组织形式，一般包的名称就是其源文件所在目录的名称，虽然Go语言没有强制要求包名必须和其所在的目录名同名，
但还是建议 <font color='cyan'>**包名 和 所在目录同名**</font>，这样结构更清晰

包可以定义在很深的目录中，包名的定义是不包括目录路径的，但是包在引用时一般使用全路径引用。比如在GOPATH/src/a/b/下定义一个包 c。
在包 c 的源码中只需声明为 <font color='cyan'>**package c**</font>，而不是声明为 <font color='cyan'>**package a/b/c**</font>，
但是在导入 c 包时，需要带上路径，例如 <font color='cyan'>** import "a/b/c" **</font>

### 包的习惯用法
+ 包名一般是 `小写的`，使用一个简短且有意义的名称。
+ 包名一般要 `和所在的目录同名` ，也可以不同，包名中不能包含-等特殊符号。
+ 包一般使用 `域名作为目录名称`，这样能保证包名的唯一性，比如 GitHub 项目的包一般会放到GOPATH/src/github.com/userName/projectName目录下。
+ 包名为 main 的包为应用程序的入口包，`编译不包含 main 包的源码文件时不会得到可执行文件`。
+ `一个文件夹下的所有源码文件只能属于同一个包`，同样属于同一个包的源码文件不能放在多个文件夹下

## 包的导入
要在代码中引用其他包的内容，需要使用 import 关键字导入使用的包。具体语法如下：
```go
import "包的路径"
```
注意事项：
+ import 导入语句通常放在源码文件开头 `包声明语句的下面`
+ 导入的包名需要使用 `双引号包裹`起来
+ 包名是从GOPATH/src/后开始计算的，使用/进行路径分隔

包的导入有两种写法，分别是单行导入和多行导入。
### 单行导入
单行导入的格式如下：
```shell
import "包 1 的路径"  
import "包 2 的路径"
```
### 多行导入
多行导入的格式如下：
```shell
import (  
    "包 1 的路径"  
    "包 2 的路径"  
)
```

## 包的导入路径
包的引用路径有两种写法，分别是 `全路径导入` 和 `相对路径导入`

### 全路径导入
包的绝对路径就是GOROOT/src/或GOPATH/src/后面包的存放路径，如下所示：
```go
import "lab/test"  
import "database/sql/driver"  
import "database/sql"
```
上面代码的含义如下：
+ test 包是自定义的包，其源码位于GOPATH/src/lab/test目录下；
+ driver 包的源码位于GOROOT/src/database/sql/driver目录下；
+ sql 包的源码位于GOROOT/src/database/sql目录下。

### 相对路径导入
相对路径只能用于导入GOPATH下的包，标准包的导入只能使用全路径导入
例如包 a 的所在路径是GOPATH/src/lab/a，包 b 的所在路径为GOPATH/src/lab/b，如果在包 b 中导入包 a ，则可以使用相对路径导入方式。示例如下:
```go
// 相对路径导入  
import "../a"
```
当然了，也可以使用上面的全路径导入，如下所示：
```go
// 全路径导入  
import "lab/a"
```

## 包的引用格式
包的引用有四种格式，下面以 fmt 包为例来分别演示一下这四种格式。

### 一、标准引用格式
```go
import "fmt"
```
此时可以用fmt.作为前缀来使用 fmt 包中的方法，这是常用的一种方式。

示例代码如下：
```go
package main

import "fmt"

func main() {
    fmt.Println("C语言中文网")
}
```
### 二、自定义别名引用格式
在导入包的时候，我们还可以为导入的包设置别名，如下所示：
```go
import F "fmt"
```
其中 F 就是 fmt 包的别名，使用时我们可以使用F.来代替标准引用格式的fmt.来作为前缀使用 fmt 包中的方法。

示例代码如下：
```go
package main

import F "fmt"

func main() {
    F.Println("C语言中文网")
}
```

### 三、省略引用格式
```go
import . "fmt"
```
这种格式相当于把 fmt 包直接合并到当前程序中，在使用 fmt 包内的方法是可以不用加前缀fmt.，直接引用。

示例代码如下：
```go
package main

import . "fmt"

func main() {
    //不需要加前缀 fmt.
    Println("C语言中文网")
}
```
### 四、匿名引用格式
在引用某个包时，如果只是希望执行包初始化的 init 函数，而不使用包内部的数据时，可以使用匿名引用格式，如下所示：
```go
import _ "fmt"
```
匿名导入的包与其他方式导入的包一样都会被编译到可执行文件中。

使用标准格式引用包，但是代码中却没有使用包，编译器会报错。如果包中有 init 初始化函数，则通过import _ "包的路径"这种方式引用包，仅执行包的初始化函数，即使包没有 init 初始化函数，也不会引发编译器报错。

示例代码如下：
```go
package main

import (
    _ "database/sql"
    "fmt"
)

func main() {
    fmt.Println("C语言中文网")
}
```
注意：
+ <font color='cyan'>**一个包可以有多个 init 函数**</font>，包加载时会执行全部的 init 函数，但并 <font color='cyan'>**不能保证执行顺序**</font>，所以不建议在一个包中放入多个 init 函数，将需要初始化的逻辑放到一个 init 函数里面。
+ <font color='cyan'>**包不能出现环形引用**</font> 的情况，比如包 a 引用了包 b，包 b 引用了包 c，如果包 c 又引用了包 a，则编译不能通过。
+ <font color='cyan'>**包的重复引用是允许**</font> 的，比如包 a 引用了包 b 和包 c，包 b 和包 c 都引用了包 d。这种场景相当于重复引用了 d，这种情况是允许的，并且 Go 编译器保证包 d 的 init 函数只会执行一次。

## 包加载
### Go 包的初始化
![/images/docImages/package.png](/images/docImages/package.png)

Go语言包的初始化有如下特点：
+ 包初始化程序从 main 函数引用的包开始，<font color='cyan'>**逐级查找包的引用**</font> ，直到找到没有引用其他包的包，最终生成一个 <font color='cyan'>**包引用的 有向无环图**</font>
+ Go 编译器会将有向无环图 <font color='cyan'>**转换为一棵树**</font> ，然后从树的叶子节点开始逐层向上对包进行初始化
+ 单个包的初始化过程如上图所示，<font color='cyan'>**先初始化常量**</font>，<font color='cyan'>**然后是全局变量**</font>，<font color='cyan'>**最后执行包的 init 函数**</font>