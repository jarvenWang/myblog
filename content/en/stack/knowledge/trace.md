---
author: "wangjinbao"
title: "直播分布式系统的一致性"
date: 2022-05-18
description: "分布式系统的一致性。"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags:
- knowledge
categories:

---
![/images/docImages/pl1.png](/images/docImages/pl1.png)
## 一、分布式理论基础
### CAP
+ Consitency(一致性)
+ Availability(可用性)
+ Partition tolerance(分区容错性)

CAP无法同时满足

![/images/docImages/pl2.png](/images/docImages/pl2.png)

### BASE理论
+ Basically Available(基本可用)
+ Soft state(软状态)
+ Eventually consistent(最终一致性)

BASE理论是对CAP理论的延伸，核心思想是即使无法做到强一致性，但应该可以采用适合的方式达到最终一致性（Eventually consistent）

### 分布式事务
>Q：什么是分布式事务？
> 顾名思义就是要在分布式系统中实现事务，它其实是由多个事务组合而成。
> 要么都成功，要么都失败，解决分布式系统中业务之间的一致性问题

![/images/docImages/pl3.png](/images/docImages/pl3.png)

![/images/docImages/pl4.png](/images/docImages/pl4.png)

### 2PC
2pc(Two-Phase-Commit)
![/images/docImages/pl5.png](/images/docImages/pl5.png)

事务管理器 分 "两个阶段" 来协调资源管理
1. 准备
2. 提交/回滚

### TCC
TCC（Try-Confirm-Cancel）服务化的两阶段，三个操作都需要编码实现
![/images/docImages/pl7.png](/images/docImages/pl7.png)

一阶段：Try
二阶段：Commit/Cancel

1. Try:检查预留的资源
2. Confirm:真正的业务提交
3. Cancel:释放预留的资源

### Saga
Saga 是一种补偿协议
1. 业务流程中每个参与者都提交本地事务，当出现某一个参与者失败则补偿前面已经成功的参与者
2. 一阶段正向服务和二阶段补偿服务都由业务开发者实现

![/images/docImages/pl8.png](/images/docImages/pl8.png)
#### 优点
+ 一阶段提交本事务，无锁，高性能
+ 事件驱动架构，参与者可异步执行，高吞吐

#### 缺点
+ 一阶段已经提交本地数据库事务，且没有进行"预留"动作，所以不能保证隔离性


## 二、送礼链路简介
![/images/docImages/pl9.png](/images/docImages/pl9.png)

营收的业务特性
1. 数据一致性 > 可用性
2. 不多发少发

>面试题：保证不多发少发？

## 三、多发少发的原因
产生的原因： 就是 <font color='pink'>**各种异常场景**</font>，也基本上是由 <font color='pink'>**幂等、事务一致性**</font> 导致
![/images/docImages/pl10.png](/images/docImages/pl10.png)


## 四、幂等
### 分布式事务的基础
用户反馈：点击送礼了一次，结果扣了两次星币，可能的原因？
![/images/docImages/pl11.png](/images/docImages/pl11.png)
>Q:幂等id由后端服务端生成，怎么理解？
> 前端通过操作id与后端的幂等id做映射，有检验，更安全

>Q:为什么要用全局id?能降级么？
> 全局id保证了全链路幂等，通过 <font color='pink'>**雪花算法**</font>，降级要基于业务场景考虑，但可能会导致冲突。

![/images/docImages/pl12.png](/images/docImages/pl12.png)

### 全局id如何生成
后端：Global ID生成（雪花算法）
雪花算法Snowflake ID有64bits长，由以下三部分组成：
![/images/docImages/pl13.png](/images/docImages/pl13.png)
![/images/docImages/pl14.png](/images/docImages/pl14.png)

#### 客户端 操作id生成方案：
SessionId统一算法:
1. SessionId算法：
   sessionId = Md5(imei + pageName + 随机盐 + 时间戳 )
```java
//以下为Android端Demo
String rawStr = MediaApplicationController.getMID() +   //imei
        this.getClass().getSimpleName() +               //pageName
        MD5Utils.getCommonSalt() +                      //随机盐
        SystemClock.elapsedRealtime();                  //时间戳
String sessionId = MD5Utils.getMd5(rawStr);
```
2. 随机盐算法：
```java
 public static String getCommonSalt() {
    Random r = new Random();
    StringBuilder sb = new StringBuilder(16);
    sb.append(r.nextInt(99999999)).append(r.nextInt(99999999));
    int len = sb.length();
    if (len < 16) {
        for (int i = 0; i < 16 - len; i++) {
            sb.append("0");
        }
    }
    return sb.toString();
}
```

#### web 操作id生成方案：
Ack重试机制幂等ID技术方案:
技术方案
+ 增加Fx.ajax拦截器，对支持幂等重试的接口增加幂等ID
+ 幂等ID作为URL参数携带，参数Key：idempotent
+ 幂等ID计算公式：UUID
```java
function uuid() {
  let d = new Date().getTime();
  if (typeof performance !== 'undefined' && typeof performance.now === 'function'){
      d += performance.now();
  }
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
      let r = (d + Math.random() * 16) % 16 | 0;
      d = Math.floor(d / 16);
      return (c === 'x' ? r : (r & 0x3 | 0x8)).toString(16);
  });
}
```
增加幂等ID前接口请求:
```shell
curl https://fx.service.kugou.com/TreasureHuntServices/TreasureHuntService/TreasureHuntV2/treasureHunt?args=[3,1031429,1300549442,3]&jsonpcallback=jsonphttpsxxx
```

增加幂等ID后接口请求（单数据中心）
```shell
# 首次走CDN
curl https://fx.service.kugou.com/TreasureHuntServices/TreasureHuntService/TreasureHuntV2/treasureHunt?args=[3,1031429,1300549442,3]&idempotent=cf1a9a7c-c204-48bc-a173-73d7ca86a9ef&jsonpcallback=jsonphttpsfxservicekugouxx
# 走北京接入重试
curl https://fxservice1.kugou.com/TreasureHuntServices/TreasureHuntService/TreasureHuntV2/treasureHunt?args=[3,1031429,1300549442,3]&idempotent=cf1a9a7c-c204-48bc-a173-73d7ca86a9ef&jsonpcallback=jsonphttpsfxservice1kugouxx
# 走广州接入专线回北京
curl https://fxservice2.kugou.com/TreasureHuntServices/TreasureHuntService/TreasureHuntV2/treasureHunt?args=[3,1031429,1300549442,3]&idempotent=cf1a9a7c-c204-48bc-a173-73d7ca86a9ef&jsonpcallback=jsonphttpsfxservice2kugouxx
```
增加幂等ID后接口请求（双数据中心）
```shell
# 已北方用户为例，南方用户首次走 fx2
 
# 首次走CDN回北京
curl https://fx1.service.kugou.com/TreasureHuntServices/TreasureHuntService/TreasureHuntV2/treasureHunt?args=[3,1031429,1300549442,3]&idempotent=cf1a9a7c-c204-48bc-a173-73d7ca86a9ef&jsonpcallback=jsonphttpsfxservicekugouxx
# 重试走北京接入
curl https://fxservice3.kugou.com/TreasureHuntServices/TreasureHuntService/TreasureHuntV2/treasureHunt?args=[3,1031429,1300549442,3]&idempotent=cf1a9a7c-c204-48bc-a173-73d7ca86a9ef&jsonpcallback=jsonphttpsfxservice3kugouxx
# 重试走CDN回广州
curl https://fx2.service.kugou.com/TreasureHuntServices/TreasureHuntService/TreasureHuntV2/treasureHunt?args=[3,1031429,1300549442,3]&idempotent=1531987904734x99303996x683693&jsonpcallback=jsonphttpsfx2servicekugouxx
# 重试直连广州
curl https://fxservice4.kugou.com/TreasureHuntServices/TreasureHuntService/TreasureHuntV2/treasureHunt?args=[3,1031429,1300549442,3]&idempotent=cf1a9a7c-c204-48bc-a173-73d7ca86a9ef&jsonpcallback=jsonphttpsfxservice4kugouxx
```

Ack提供获取幂等ID函数
增加幂等ID
```java
// 获取幂等ID
function getIdempotent() {
  return {
    key: IDEMPOTENT_KEY,
    val: uuid()
  }
}
```

### 经典幂等问题
![/images/docImages/pl15.png](/images/docImages/pl15.png)
![/images/docImages/pl16.png](/images/docImages/pl16.png)
![/images/docImages/pl17.png](/images/docImages/pl17.png)



## 五、分布式事务一致性方案
### 基于回查
#### 目前营收90%以上的业务使用了该方案，解决营收top10的业务丢单问题
![/images/docImages/pl18.png](/images/docImages/pl18.png)

### 基于本地事务消息表
基于Pulsar的可靠消息最终一致性(at least once)，目前在主推
![/images/docImages/pl19.png](/images/docImages/pl19.png)

![/images/docImages/pl20.png](/images/docImages/pl20.png)

### 最大努力通知型
![/images/docImages/pl21.png](/images/docImages/pl21.png)


## 六、mysql集群一致性
### 直播mysql集群部署简介

#### 南北双活（MHA + Consul + Otter）
![/images/docImages/pl22.png](/images/docImages/pl22.png)

#### 同城主备-依赖 MHA + Consul 实现 高可用
![/images/docImages/pl23.png](/images/docImages/pl23.png)

>Q:直接mysql目前采用复制方式是哪种？
> 异步

![/images/docImages/pl24.png](/images/docImages/pl24.png)
+ 单机房写，强一致性，但当master故障、机房网络故障或脑裂时，mysql数据未复制到新master，MHA强制切换可能会造成数据不一致
+ 故障切换时一致性和不可用时长需要取舍
+ 核心是：<font color='pink'>**止损**</font>

mysql集群一致性问题：都可归为 <font color='pink'>**多副本数据不一致**</font> 的问题
遇到的经典问题：
![/images/docImages/pl25.png](/images/docImages/pl25.png)
线上问题更多的是 <font color='pink'>**读方案**</font> 的问题，而<font color='pink'>**不是MHA切换**</font>  的问题
### 复盘守的问题
![/images/docImages/pl26.png](/images/docImages/pl26.png)

总结：
1. 业务需评估是否为高实时业务
2. 修改表结构需评估主从延时
3. 业务要判断主从延时的影响

## 七、缓存与DB的一致性
### 常用的缓存模式
![/images/docImages/pl27.png](/images/docImages/pl27.png)
方法：
+ JOB定时将数据从DB同步到Cache中
+ 应用直接访问cache，不回查数据库

![/images/docImages/pl28.png](/images/docImages/pl28.png)
方法：
+ 失效：应用程序先从Cache取数据，没有得到，则从数据库中取数据，成功后，放到缓存中
+ 命中：应用程序从Cache中取数据，取到后返回
+ 更新：先把数据存到数据库中，成功后，再让缓存失效

>Q1:什么情况下会使用缓存？
> 挡量、提高访问速度、使用简单

>Q2:缓存与DB的一致性怎么保障？
> 1. 涉及到两个系统，那么必然会存在数据一致性的问题
> 2. 基于Q1的回答及CAP的理论，要么通过2PC或是Paxos协议保证数据的强一致性，要么就是接受数据的最终一致性

![/images/docImages/pl29.png](/images/docImages/pl29.png)
### 案例
#### DB同步是采用otter、采用nsq通知来清除redis的数据
![/images/docImages/pl30.png](/images/docImages/pl30.png)

两种同步/通知机制
1. 异地查询脏数据
2. DB与缓存（有效期间内）
最终一致性无法保证

> 怎么办？
> 收到消息线程sleep?
> 延时队列？

解决方案：
采用同一种机制--基于otter
![/images/docImages/pl31.png](/images/docImages/pl31.png)

缺点：
otter意向回环，非主机房nsq流量翻倍

基于otter做数据变更模型：
在otter--node执行SETL的L阶段（load阶段）进行数据推送
![/images/docImages/pl32.png](/images/docImages/pl32.png)
## 八、分布式锁
### 分布式锁主要的三种实现方式
#### 基于DB

乐观锁
+ 版本号字段
+ 基于CAS
+ 不具有互斥性
+ 不会产生锁等待

悲观锁
+ select ... where ... for update
+ 索引、锁表

#### 基于redis
分布式锁
![/images/docImages/pl33.png](/images/docImages/pl33.png)

redis分布式锁
+ 性能高
+ 锁失效
+ 多机房情况


#### 基于ZK
分布式锁
![/images/docImages/pl34.png](/images/docImages/pl34.png)

### 应用场景
消费服务更新余额：
![/images/docImages/pl35.png](/images/docImages/pl35.png)

+ redis 是无法真正锁住的（挡量）
+ 数据库进行兜底