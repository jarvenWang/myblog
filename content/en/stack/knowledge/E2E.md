---
author: "wangjinbao"
title: "全链路压测实践"
date: 2022-05-19
description: "全链路压测（End-to-End（E2E） Performance Testing）是指对软件系统或服务进行综合性能测试的一种方法。"
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
## 全链路压测
### 概念
全链路压测（End-to-End（E2E） Performance Testing）
是指对软件系统或服务进行综合性能测试的一种方法。它模拟了真实的用户场景和环境，从用户端到服务器端的整个链路进行测试，包括用户界面、网络传输、服务器处理、数据库访问等环节。
### 目标
是评估系统在高负载和复杂场景下的性能表现，找出性能瓶颈和潜在的问题，以便优化系统的性能和稳定性。通过模拟大量的并发用户访问、持续高负载、复杂数据操作等情况，可以检测系统在真实应用场景下的性能指标，例如响应时间、并发处理能力、吞吐量、资源利用率等指标。

## 全链路压测的演进史
QQ音乐和酷狗全链路压测的演进史
### QQ音乐
![/images/docImages/jm1.png](/images/docImages/jm1.png)

### 酷狗音乐
![/images/docImages/jm1.png](/images/docImages/jm1.png)

### 微服务下的挑战
![/images/docImages/jm3.png](/images/docImages/jm3.png)

### 线上服务性能挑战
![/images/docImages/jm4.png](/images/docImages/jm4.png)
### 全链路压测
![/images/docImages/jm5.png](/images/docImages/jm5.png)
### 全链路压测系统核心功能
![/images/docImages/jm6.png](/images/docImages/jm6.png)
### QQ音乐全链路压测系统架构
![/images/docImages/jm7.png](/images/docImages/jm7.png)
### 酷狗线上流量压测系统架构
![/images/docImages/jm8.png](/images/docImages/jm8.png)

## 全链路压测平台的介绍
### 一个基本的压测流程
![/images/docImages/jm9.png](/images/docImages/jm9.png)
#### 用例准备阶段
![/images/docImages/jm10.png](/images/docImages/jm10.png)
#### 用例准备阶段-场景编排
![/images/docImages/jm11.png](/images/docImages/jm11.png)
#### 数据准备阶段-生产环境流星获取
![/images/docImages/jm12.png](/images/docImages/jm12.png)
#### 数据准备阶段-自定义流星获取
![/images/docImages/jm13.png](/images/docImages/jm13.png)
#### 压测执行阶段-链路发现
![/images/docImages/jm14.png](/images/docImages/jm14.png)
#### 压测执行阶段-标记透传
![/images/docImages/jm15.png](/images/docImages/jm15.png)
#### 压测执行阶段-存储隔离
![/images/docImages/jm16.png](/images/docImages/jm16.png)

#### 压测执行阶段-实时监控
![/images/docImages/jm17.png](/images/docImages/jm17.png)
#### 服务器资源占用监控
##### QQ音乐运维平台
![/images/docImages/jm18.png](/images/docImages/jm18.png)
##### 酷狗KMC监控系统
![/images/docImages/jm19.png](/images/docImages/jm19.png)

#### 应用性能监控
##### QQ音乐模调系统
![/images/docImages/jm20.png](/images/docImages/jm20.png)
##### 酷狗APM监控系统
![/images/docImages/jm21.png](/images/docImages/jm21.png)

#### 压测执行阶段-安全性保障
![/images/docImages/jm22.png](/images/docImages/jm22.png)

#### 压测报告查看
##### QQ音乐
![/images/docImages/jm23.png](/images/docImages/jm23.png)
##### 酷狗
![/images/docImages/jm24.png](/images/docImages/jm24.png)




## 实践案例分享
### 社交属性业务特性与挑战
业务特性： <font color='cyan'>**突增流量、高并发读写、实时性**</font>
![/images/docImages/jm25.png](/images/docImages/jm25.png)

### 实例分享-时代少年团空降评论区
![/images/docImages/jm26.png](/images/docImages/jm26.png)
![/images/docImages/jm27.png](/images/docImages/jm27.png)

#### 迭代优化方案
+ <font color='cyan'>**热key问题**</font>
+ <font color='cyan'>**缓存命中率**</font>
+ <font color='cyan'>**缓存防穿透**</font>
+ <font color='cyan'>**业务降级策略**</font>
+ <font color='cyan'>**中间件**</font>
+ <font color='cyan'>**业务机器扩容**</font>

  ![/images/docImages/jm28.png](/images/docImages/jm28.png)
  ![/images/docImages/jm29.png](/images/docImages/jm29.png)

## 总结
![/images/docImages/jm30.png](/images/docImages/jm30.png)


