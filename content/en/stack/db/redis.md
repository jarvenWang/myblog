---
author: "Karson"
title: "redis应用问题与解决方案"
date: 2022-08-26
description: "redis应用问题与解决方案"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: Karson
authorEmoji: 👻
tags:
- db
categories:
- redis

---
## redis
### redis的好处

+ 高性能
+ 高并发

### redis的问题场景
1. 读写不致场景
2. 穿透雪崩场景
3. 并发竞争场景

### redis的数据结构

#### String-字符串
存储json等字段
#### Hash-字典
存储field-value映射的<font color='cyan'>对象</font>
#### List-列表
存储 <font color='cyan'>有顺序、可重复</font>的集合
#### Set-集合
无序，<font color='cyan'>不能重复</font>的等数据，redis本身操作交集、并集、差集等操作
#### zSet-有序集合
<font color='cyan'>有序的</font>，常用排行榜场景

### redis的线程模型
单线程还很快的原因：<font color='cyan'>IO多路复用，同时监听/处理客户端多个socket请求</font>，并把socket放入队列中，由文件事件分配器分配给 
应答处理器、命令请求处理器、命令回复处理器等多个处理器可以并发处理，基于这种线程模型redis处理速度非常快。

### redis的过期策略
+ 惰性过期策略：
key没有设置过期时间，不会删除，导致内存中存储大量的过期key
+ 定期过期策略：
每隔一段时间检查过期key并删除

### redis的内存淘汰策略
"不淘汰、过期时间、最近最少、随机、全局最近最少、全局随机"
+ 不淘汰策略：保证数据完整性，内存不足写直接报错
+ 过期时间优先：从设置了过期时间的key中，优先删除剩余时间最小的key
+ 最近最少使用：从设置了过期时间的key中，优先删除最久没有使用的key
+ 随机删除：从设置了过期时间的key中，随机删除
+ 全局最近最少：无论是否有过期时间，选择最少使用的key删除
+ 全局随机：所有key中随机删除

### redis的哨兵机制
redis2.8之后引入哨兵机制，自动把主从架构(master-slave)中的从切换成主
哨兵集群 sentinel
#### 哨兵访问流程
1. <font color='cyan'>客户端-->访问哨兵节点/集群获取redis主节点</font>
2. <font color='cyan'>访问redis主节点-->redis主节点下线</font>
3. <font color='cyan'>哨兵进行主从切换-->哨兵告诉客户端主节点-->访问新的主节点</font>

> PS: <font color='cyan'>哨兵选举</font>：内部发起投票请求，<font color='cyan'>投票多的成为领导者</font>，主导redis主从切换

### 缓存击穿
场景：高并发的场景下，<font color='cyan'>一个热点key失效</font>时，大量请求直接打在mysql上
#### 解决一：热点key永不过期
#### 解决二：请用互斥锁
同一时间只有一个线程打到了mysql，更新key后，其它线程正常访问redis


### 缓存雪崩(缓存宕机)
场景：高并发的场景下，<font color='cyan'>大量的缓存失效或缓存宕机</font>，大量请求直接打在mysql上
#### 解决一：key过期时间随机
#### 解决二：请用互斥锁
#### 解决三：先查本地缓存，再查redis，再查mysql
#### 解决四：redis集群

### 缓存穿透
恶意请求，<font color='cyan'>请求缓存和数据库中不存在的数据</font>，每次请求都访问数据库
#### 解决一：缓存空对象+合理过期时间
#### 解决二：非法参数校验
#### 解决三：布隆过滤器
可以快捷的判断某个数据属于哪个集合，过滤掉大量请求，相当于白名单、黑名单的概念

## redis的持久化方案
RDB和AOF的持久化机制
+ RDB ：内存快照
+ AOF ：增量日志
+ RDB+AOF ：混合

### RDB
每隔一段时间把<font color='cyan'>内存数据以快照方式写入磁盘，全量备份</font>
#### RDB持久化流程
主进程fork一个子进程对共享数据进行备份，主进程继续处理客户端请求
自动触发：
+ 配置触发：
`save 100 10`
+ shutdown触发：
`shutdown`
+ flush触发：
清空redis的同时，删除dump.rdb文件

#### RDB优缺点
优点：
+ 高性能：恢复数据快
+ 文件体积小：二进制文件

缺点：
+ 丢失数据：备份时间间隔


### AOF
把redis的请求写到 <font color='cyan'>AOF缓冲区</font>，<font color='cyan'>同步到AOF日志文件</font>中，AOF重写压缩日志

#### AOF同步策略
+ 每次redis写操作，都同步到日志文件
+ 每秒刷新AOF缓冲区的数据到AOF文件
+ 交给操作系统判断，redis不主动刷盘

#### AOF优缺点
优点：
+ 数据更可靠：每个命令都记录
+ 可保留历史

缺点：
+ 文件较大
+ 恢复比较慢

>PS:总结
> + 推荐两者都开启
> + 数据不敏感，单独用RDB
> + 不建议单独用AOF，容易bug

## redis问题场景及解决
### 保证redis与数据库数据一致性?
> PS ：只读取数据，不会出现数据不一致的情况

#### 写(更新)数据流程：
方法：
+ 先更新缓存再更新数据库(略)
+ 先<font color='cyan'>删除缓存</font>再更新数据库
+ 先更新数据库再更新缓存(略)
+ 先更新数据库再<font color='cyan'>删除缓存</font>

>ps : 删除缓存：理解很简单，一个线程删除缓存后，更新数据库再更新缓存，下次就有缓存了
#### 方式一：先操作缓存-延迟双删
读 和 写 并发操作场景：
| 线程一         | 线程二       |
|-------------|-----------|
| 1删除缓存       |           |
| 2修改数据库，网络延迟 |           |
|             | 3查数据      |
|             | 4缓存没有查数据库 |
| 5数据库(新)数据   |           |
|             | 6缓存(老)数据  |
|      &&&&&       | &&&&&  |
|      7删除缓存       |   |
|             |  8只有这次是老数据 |
|      之后所有线程都是新数据       |   |

以上是<font color='cyan'>"最终一致性"</font>方案，AP原则

如果用<font color='cyan'>"强一致性"</font>方案，加锁，性能不好，CP原则

总结解决方案：<font color='cyan'>**延迟双删**</font>

#### 方式二：先操作数据库(推荐)-利用MQ

| 线程一    | 线程二       | 线程三       |
|--------|-----------|-----------|
| 1修改数据库 |           |           |
| 2(新)数据 |           |           |
|        | 3查缓存(旧)数据 |           |
| 4删除缓存  |           |           |
|        |      | 5查数据库更新缓存 |
|        |      | 6缓存(新)数据  |

极端场景删除缓存失败：
删除重试的机制，利用MQ异步消息，会有代码耦合
>PS:阿里的开源框架canal 订阅binlog异步删除






### 什么情况redis集群不可用?
理论：为保证集群完整性，默认当集群16384个槽没有分配到任何节点上，整个集群不可用。
#### 1.访问一个master和slave都挂了
#### 2.集群半数宕机
#### 3.某个节点处理不均衡

### 如何提高缓存命中率?
#### 1.架构设计方面：缓存维度、缓存颗粒度
#### 2.缓存数据：预加载，更新频率，增容量

### redis集群写操作会丢失吗？
#### 1.异步复制数据
#### 2.哨兵机制节点不是奇数，选举失败

### redis哈希槽？
哈希槽本质上就是一个数组空间，Redis Cluster中哈希槽的其长度为 2^14=16384
，数据经过CRC16哈希算法后再取模落到16384个槽位上.
16384：节点之间会定期发送ping/pong消息，集群节点过多过少都会影响，作者最后定的16384
#### 数据分布理论
把数据集分配到各个节点，每个节点负责整个子集
#### 常见数据分区规则
哈希分区：离散度好，无序，与业务无关
顺序分区：离散度易倾斜，有序，与业务有关


>PS: redis集群的高可用则是依赖 <font color='cyan'>节点的主从复制及主从之间的故障转移</font>。


### redis常见的性能问题？解决方案？
#### 1持久化性能问题
解决方案：
1. 全量复制，用部分复制(一台机)
2. 主从，主不做持久化，从做持久化

#### 2数据比较重要
解决方案：
1. AOF持久化，从开启AOF，策略每秒同步一次

#### 3主从网络通畅
解决方案：
1. 主从同一局域网

#### 4主压力大
解决方案：
1. 减少从库，减少主库备份操作

#### 5主从不用网络结构
解决方案：
1. 采用线型结构