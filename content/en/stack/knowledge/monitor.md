---
author: "wangjinbao"
title: "监控体系演进及可视化"
date: 2022-05-18
description: "监控体系演进及可视化。"
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
## 监控流程
![/images/docImages/mr1.png](/images/docImages/mr1.png)
![/images/docImages/mr2.png](/images/docImages/mr2.png)

### 数据上报
![/images/docImages/mr3.png](/images/docImages/mr3.png)
### 数据处理
![/images/docImages/mr4.png](/images/docImages/mr4.png)
### 接入使用
#### 公有云建设（独立部署，功能闭环）
+ 功能优化：
1. HTTP接口，支持客户端外网上报
2. DC Agent，兼容基础指标上报
3. 告警通道：
    - 企业微信（拉群助手）
    - 微信（企业号）
    - 邮件（覆盖全员）
+ 建设成果：
1. 首次10min内全流程跑通，后续3min内
2. 酷我17数据流、13看板、60亿+/天
3. 懒人12数据流、1看板、3亿+/天
4. QQ音乐业务线20数据流、11看板
![/images/docImages/mr5.png](/images/docImages/mr5.png)

### 数据感知-查询
#### 多维能力
+ 多个维度筛选查询
+ 支持拆线图、比例图

![/images/docImages/mr6.png](/images/docImages/mr6.png)

#### 对比分析
+ 多维度对比、比较差异
+ 定位优化方向

![/images/docImages/mr7.png](/images/docImages/mr7.png)


#### 地图分布
+ 客户端质量全局掌控
+ 支持下钻查询
  ![/images/docImages/mr8.png](/images/docImages/mr8.png)

#### 自定义看板
+ 自主配置Dashboard
+ 支持多种图表：拆线、柱状、饼、表格、漏斗等
  ![/images/docImages/mr9.png](/images/docImages/mr9.png)


### 数据感知-告警
#### 常规模调告警
模块间调用，点对点关系
缺乏扩展关联性
![/images/docImages/mr10.png](/images/docImages/mr10.png)

#### 微服务Trace扩展
OpenTelemetry规范
统一Jaeger Agent采集
链路精准可靠
告警关联变更
上下游变更周知
![/images/docImages/mr11.png](/images/docImages/mr11.png)

#### 告警链路
大规模告警
+ 短时间发生大量模调告警
+ 分析告警关联性
+ 实际告警链路
+ 智能分析
1. 聚集分析
2. 关联存储
3. 关联变更
4. 关联告警
5. 陡增陡降
6. 返回码信息
7. 压测
8. 腾讯云公告

![/images/docImages/mr12.png](/images/docImages/mr12.png)

### 数据应用-业务视图
#### 以业务特性为维度进行管理
+ 关联相关各种资源
+ 全局视角把控服务质量
+ 快速变更、容量管理

![/images/docImages/mr13.png](/images/docImages/mr13.png)

#### 活动监控
+ 分钟级实时监控
1. 负载、流量、成功率、告警
+ 异常数据，动态排序
+ 可接入多种第三方监控
1. Grafana
2. 腾讯云监控
3. 自定义看板

![/images/docImages/mr14.png](/images/docImages/mr14.png)