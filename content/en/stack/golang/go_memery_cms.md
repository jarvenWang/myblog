---
author: "wangjinbao"
title: "Golang的内存管理"
date: 2022-02-05 00:00:01
description: "Golang 的内存管理"
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
## 思想
### 1.以空间换时间，一次缓存，多次复用
go中的堆 mheap 正是"缓存"的思想。
堆mheap的特点：
+ 对于操作系统，是用户进程中缓存的内存
+ 对于go进行内部，堆是所有对象的内存起源

### 2.多级缓存，实现无/细锁化
![/images/docImages/mem1.png](/images/docImages/mem1.png)

堆 是Go运行时中最大的临界共享资源，这意味着每次存取都要加锁，在性能层面是一件很可怕的事情。

相关概念：
1. <font color='pink'>mheap</font> : 全局的内存起源，访问要加全局锁
2. <font color='pink'>mcentral</font> : 每种对象大小规格（全局共划分为68种）对应的缓存，锁的粒度也仅限于同一种规格以内
3. <font color='pink'>mcache</font> ：每个P（正是GMP中的P）持有一份的内存缓存，访问时无锁

### 3.多级规格，提高利用率
--> span大小趋势递增 
--> 分配object大小趋势递增
相关概念：
1. <font color='pink'>page</font> ： 最小的存储单元
go借鉴操作系统分页管理的思想，每个最小的存储单元也称之为页 <font color='pink'>**page**</font> ，但大小为 <font color='pink'>**8KB**</font>
2. <font color='pink'>mspan</font> : 最小的管理单元
mspan大小为page的整数倍，且从8B到80KB被划分为 68 种不同的规格，分配对象时，会根据大小映射到不同规格的mspan，从中获取空间

## 内存单元mspan
mspan中包括：next、prev指针、startAddr、allocCache（0表示被对象占用，1是空闲的 ）、pages

mspan类的源码位于 runtime/mheap.go文件中：
```go
type mspan struct {
    //标识前后节点的指针
	next *mspan     // next span in list, or nil if none
	prev *mspan     // previous span in list, or nil if none
	//起始地址
	startAddr uintptr // address of first byte of span aka s.base()
	//包含几页，页是连续的
	npages    uintptr // number of pages in span
	//标识此前的位置都已被占用
	freeindex uintptr
	//最多可以存放多少个object
	nelems uintptr // number of object in the span.
	//bitmap每个bit对应一个object块，标识该块是否已被占用
	allocCache uint64
	//标识mspan等级，包含class和noscan两部分信息
	spanclass             spanClass     // size class and noscan (uint8)
}
```

mspan等级的源码 runtime/sizeclasses.go中：

```shell
// class  bytes/obj  bytes/span  objects  tail waste  max waste  min align
//     1          8        8192     1024           0     87.50%          8
//     2         16        8192      512           0     43.75%         16
//     3         24        8192      341           8     29.24%          8
//     4         32        8192      256           0     21.88%         32
//     5         48        8192      170          32     31.52%         16
//     6         64        8192      128           0     23.44%         64
```
最大浪费率：
`（（24-77）*341 + 8）8192 = 29.24% `

代码位于 runtime/mheap.go

## 线程缓存mcache
特点：
1. mcache是每个P独有的缓存，因些交互无锁
2. mcache将每种spanClass等级的mspan各缓存一个，总数为2（noscan维度）*68（大小维度）=136
3. mcache中还有一个为对象分配器tiny allocator，用于处理小于16B对象的内存分配

mcache代码位于runtime/mcache.go:
```go
type mcache struct {
    //微对象分配器相关
    tiny       uintptr
	tinyoffset uintptr
	tinyAllocs uintptr
	
	//mcache中缓存的mspan，每种spanClass各一个
	alloc [numSpanClasses]*mspan
}
```

## 中心缓存mcentral
特点：
1. 每个mcentral对应一种spanClass
2. 每个mcentral下聚合了该spanClass下的mspan
3. mcentral下的mspan分为两个链表，分别为有空间mspan链表partial和满空间mspan链表full
4. 每个mcentral一把锁

代码位于runtime/mcentral.go:
```go
type mcentral struct {
	//对应的spanClass
	spanclass spanClass
    //有空位的mspan集合，数组长度为2是用于抗一轮GC
	partial [2]spanSet // list of spans with a free object
	//无空位的mspan集合
	full    [2]spanSet // list of spans with no free objects
}
```

## 全局堆缓存mheap
要点：
1. 对于go上层应该而言，堆是操作系统虚拟内存的抽象
2. 以页（8KB）为单位，作为最小内存存储单元
3. 负责将连续页组装成mspan
4. 全局内存基于bitMap标识其使用情况，每个bit对应一页，为0则自由，为1则已被mspan组装
5. 通过heapArena聚合页，记录了页到msapn的映射信息
6. 建立空闲页基数树索引radix tree index，辅助快速寻找空闲页
7. 是mcentral的持有者，持有所有spanClass下的mcentral，作为自身的缓存
8. 内存不够时，向操作系统申请，申请单位为heapArena

代码在 runtime/mheap.go中
```go
type mheap struct {
    //堆的全局锁
    lock mutex
    //空闲页分配器，底层是多棵基数树组成的索引，每棵树对应16GB内存空间
	pages pageAlloc
	//记录了所有的mspan，需要知道，所有mspan都是经由mheap，使用连续空闲页组装
	allspans []*mspan // all spans out there
	//heapAreana数组，64位系统下，二维数据容量为 [1][2^22]
	//每个heapArena大小64M，因此理论上，Golang堆上限为2^22*64M=256T
	arenas [1 << arenaL1Bits]*[1 << arenaL2Bits]*heapArena
	
	// 多个mcentral，总个数为spanClass的个数
	central [numSpanClasses]struct {
		mcentral mcentral
		//用于内存地址对齐
		pad      [(cpu.CacheLinePadSize - unsafe.Sizeof(mcentral{})%cpu.CacheLinePadSize) % cpu.CacheLinePadSize]byte
	}
}
```

## 空闲页索引pageAlloc
基数树数据结构的含义：
1. mheap会基于 bitMap 标识内存中各页的使用情况，<font color='pink'>**bit位为 0 ，代表该页是空闲的，为 1 代表该页已被mspan占用**</font>。
2. 每棵基数树聚合了 <font color='pink'>**16GB**</font> 内存空间中各页使用情况的索引信息，用于帮助mheap快速找到指定长度的连续空闲页的所在位置
3. mheap持有 <font color='pink'>**2^14**</font> 棵基数树，因此索引全面覆盖到 <font color='pink'>**2^14 * 16GB = 256 T**</font> 的内存空间。



## heapArena
特点：
+ 每个heapArena 包含 8192 个页，大小为 8192 * 8KB = 64 MB
+ heapArena记录了页到mspan的映射，因为GC时，通过地址偏移找到页很方便，但找到其所属的mspan不容易，因些需要通过这个映射信息进行辅助
+ heapArena是mheap向操作系统申请内存的单位（64MB）

代码位于runtime/mheap.go
```go
const pagesPerArena = 8192

type heapArena struct{
    //实现page到mspan的映射
    spans [pagesPerArena]*mspan
}
```

## object的大小分三类
+ tiny微对象 (0,16B)
+  small小对象 (16B,32KB)
+ large小对象 (32KB,*)->0等级