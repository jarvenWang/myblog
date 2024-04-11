---
author: "wangjinbao"
title: "云原生观测性"
date: 2022-05-17
description: "观测性的大三要素，监控，日志，链路。"
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

应用的稳定性，以及出现问题的时候，怎样的快速定位到真正的原因，对于很多企业来说是都是一直在不断的设计和完善的能力。主要体现在怎样监控系统，怎样从日志上快速的找到错误，怎样快速的知道调用链是不是出现了问题，以及应用的运行时有没有出问题，应用依赖的数据库，中间件等是不是出现问题了。

![/images/docImages/mt1.jpg](/images/docImages/mt1.jpg)

图上介绍了Metrics 、 Tracing 、 Logging 的定义和关系，三者都有各自发挥的空间，每种数据都没办法完全被其他数据代替。


## 观测性方案
常用方案总结：
### 日志方案：
`filebeats->elasticsearch->kibana；`
`filebeats->kafka->logstash->elasticsearch->kibana; `
`fluentd->elasticsearch->kibana；`
`fluentd->kafka->fluentd->elasticsearch->kibana；`

### 监控方案：
比较统一的都在使用 <font color='cyan'>**prometheus**</font> ，<font color='cyan'>**prometheus-operator**</font> ，<font color='cyan'>**thanos**</font>，<font color='cyan'>**grafana**</font>

### 链路方案:
主要方案有开源的 <font color='cyan'>**skywalking**</font>，<font color='cyan'>**jaeger**</font>，<font color='cyan'>**zipkin**</font>，还有商业的方案，如 <font color='cyan'>**dynatrace**</font> 等。

