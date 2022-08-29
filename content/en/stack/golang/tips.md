---
author: "wangjinbao"
title: "golang注意事项"
date: 2022-06-12T12:00:06+09:00
description: "golang开发中的一些小注意点整理"
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
image: images/feature3/img_3.png
---

## 1、左大括号 { 不能单独放一行
在其他大多数语言中，{ 的位置你自行决定。Go比较特别，遵守分号注入规则（automatic semicolon injection）：编译器会在每行代码尾部特定分隔符后加;来分隔多条语句，比如会在 ) 后加分号：
```golang
// 错误示例
func main()
{
println("hello world")
}
// 等效于
func main();    // 无函数体
{
println("hello world")
}

./main.go: missing function body
./main.go: syntax error: unexpected semicolon or newline before {

// 正确示例
func main() {
println("hello world")
}
```
## 2、未使用的变量
如果在函数体代码中有未使用的变量，则无法通过编译，不过 <font color="red">全局变量</font> 声明但<font color="red">不使用</font>是
<font color="red">可以的</font>。即使变量声明后为变量赋值，依旧无法通过编译，需在某处使用它：
```golang
// 错误示例
var gvar int     // 全局变量，声明不使用也可以

func main() {
    var one int     // error: one declared and not used
    two := 2    // error: two declared and not used
    var three int    // error: three declared and not used
    three = 3        
}


// 正确示例
// 可以直接注释或移除未使用的变量
func main() {
    var one int
    _ = one

    two := 2
    println(two)

    var three int
    one = three

    var four int
    four = four
}
```
## 3、未使用的 import
如果你 import一个包，但包中的变量、函数、接口和结构体一个都没有用到的话，将编译失败。可以使用<font color="red"> _下划线</font>符号作为别
名来忽略导入的包，从而避免编译错误，这只会执行 package 的 init()
```golang
// 错误示例
import (
    "fmt"    // imported and not used: "fmt"
    "log"    // imported and not used: "log"
    "time"    // imported and not used: "time"
)

func main() {
}


// 正确示例
// 可以使用 goimports 工具来注释或移除未使用到的包
import (
    _ "fmt"
    "log"
    "time"
)

func main() {
    _ = log.Println
    _ = time.Now
}
```
## 4、获取命令⾏参数
与其他主要编程语⾔的差异
1. main 函数不⽀持传⼊参数func main(arg []string )
2. 在程序中直接通过 os.Args 获取命令⾏参数

## 5、编写测试程序
1. 源码⽂件以 _test 结尾：xxx_test.go 
2. 测试⽅法名以 Test 开头：func TestXXX(t *testing.T) {…}

## 6、变量赋值
1. 赋值可以进⾏⾃动类型推断
2. 在⼀个赋值语句中可以对多个变量进⾏同时赋值

基本数据类型:
{{<boxmd>}}
类型
1 bool
2 string
3 int int8 int16 int32 int64
4 uint uint8 uint16 uint32 uint64 uintptr
5 byte // alias for uint8
6 rune // alias for int32,represents a Unicode code point
7 float32 float64
8   complex64 complex128
{{</boxmd>}}

## 7、⽤ == 比较数组
+ 相同维数且含有相同个数元素的数组才可以⽐较
+ 每个元素都相同的才相等

## 8、简短声明的变量只能在函数内部使用
```golang
// 错误示例
myvar := 1    // syntax error: non-declaration statement outside function body
func main() {
}


// 正确示例
var  myvar = 1
func main() {
}
```

## 9、使用简短声明来重复声明变量
不能用简短声明方式来单独为一个变量重复声明，:=左侧至少有一个新变量，才允许多变量的重复声明：
```golang
// 错误示例
func main() {  
    one := 0
    one := 1 // error: no new variables on left side of :=
}


// 正确示例
func main() {
    one := 0
    one, two := 1, 2    // two 是新变量，允许 one 的重复声明。比如 error 处理经常用同名变量 err
    one, two = two, one    // 交换两个变量值的简写
}
```

## 10、map遍历是顺序不固定
map是一种hash表实现，每次遍历的顺序都可能不一样。
Go 的运行时是有意打乱迭代顺序的，所以你得到的迭代结果可能不一致。但也并不总会打乱，得到连续相同的 5 个迭代结果也是可能的
```golang
func main() {
    m := map[string]string{
        "1": "1",
        "2": "2",
        "3": "3",
    }

    for k, v := range m {
        println(k, v)
    }
}

```

## 11、自增和自减运算
很多编程语言都自带前置后置的 ++、– 运算。但 Go 特立独行，去掉了前置操作，同时 ++、— 只作为运算符而非表达式。
```golang
// 错误示例
func main() {
    data := []int{1, 2, 3}
    i := 0
    ++i            // syntax error: unexpected ++, expecting }
    fmt.Println(data[i++])    // syntax error: unexpected ++, expecting :
}


// 正确示例
func main() {
    data := []int{1, 2, 3}
    i := 0
    i++
    fmt.Println(data[i])    // 2
}
```

## 12、运算符的优先级
除了位清除（bit clear）操作符，Go 也有很多和其他语言一样的位操作符，但优先级另当别论。
优先级列表：
|Precedence|Operator|
|-|-------:|
|5|* / % << >> & &^|
|4|+ - | ^|
|3|== != < <= > >=|
|2|&&|
|1| 2个竖线 |

## 13、new() 与 make() 的区别
new(T) 和 make(T,args) 是 Go 语言内建函数，用来分配内存，但适用的类型不同。

<font color="red">new(T) </font>会为 T 类型的新值分配已置零的内存空间，并返回地址（指针），即类型为 *T 的值。换句话说就是，返回一个指针，该指针指向新分配的、类型为 T 的零值。适用于值类型，如数组、结构体等。
<font color="red">make(T,args)</font> 返回初始化之后的 T 类型的值，这个值并不是 T 类型的零值，也不是指针 *T，是经过初始化之后的 T 的引用。make() 只适用于 slice、map 和 channel.

## 14、gin-从中间件将参数传递给路由控制器

```golang
func MiddleWare() gin.HandlerFunc {
    return func(c *gin.Context) {
        c.Set("request", "test_request")
    }
}

 router.Use(MiddleWare()){
     router.GET("/middleware", func(c *gin.Context) {
      // 两种方式都可以获取
         request := c.MustGet("request").(string)
         request2, _ := c.Get("request")
         c.JSON(http.StatusOK, gin.H{
             "request": request,
             "request2": request2,
         })
     })
 }
```

