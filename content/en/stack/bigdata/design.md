---
author: "wangjinbao"
title: "数仓模型设计原则"
date: 2023-06-24
description: "明确业务需求、清晰数据架构、有效维度建模、模块化可维护、数据质量保证、保证数据安全、可操作性和易用性"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags:
- bigdata
- categories:

---
>PS:数仓的设计流程：
> 步骤一：了解业务流程

# 建设过程：
![/images/docImages/design.png](/images/docImages/design.png)

# 一、数据整合及管理体系
## 定位与价值
建设统一的、规范化的数据接入层（ODS）和 数据中间层（DWD 和 DWS），通过 数据服务 和 数据产品
## 体系架构
包括三块：
1. 维表
2. 事实表
3. 指标

![/images/docImages/design2.png](/images/docImages/design2.png)

## 名词术语
1. 主题域（数据域）：
2. 业务过程：
3. 时间周期；
4. 修饰类型
5. 修饰词：
6. 度量：原子指标
7. 维度
8. 维度属性
9. 派生指标

## 指标体系
包括：原子指标、派生指标、修饰类型、修饰词、时间周期。

`派生指标 = 原子指标 + 时间周期 + 修饰词`
 
阿里巴巴常用的时候周期修饰词如下表：

| 中文名    | 英文名 |
|--------|--|
| 最近1天   | 1d |
| 最近3天   | 3d |
| 最近7天   | 1w |
| 最近14天  | 2w |
| 最近30天  | 1m |
| 最近60天  | 2m |
| 最近90天  | 3m |
| 最近180天 | 6m |
| 自然周    | cw |
| 自然月    | cm |
| 自然季度   | cq |
| 截至当日   | td |
| 财年     | fy |
| 最近1小时  | 1h |
| 准实时    | ts |
| 未来7天   | f1w |
| 未来4周   | f4w |

## 模型设计
### 数仓分层
数据模型的维度设计 主要以 维度建模理论 为基础，基于维度数据模型总线架构，构建一致性的 维度 和 事实。

数据模型一般分为三层：<font color="lightgreen">操作数据层（ODS）、公共维度模型层（CDM） 和 应用数据层（ADS）,其中公共维度模型层包括 明细数据层（DWD）和汇总数据层（DWS）</font>

#### 操作数据层（ODS）
定义：把操作系统数据几乎无处理地存放在数据仓库中
+ 同步：结构化数据增量或全量同步到 MaxCompute
+ 结构化：非结构（日志）结构化处理并存储到 MaxCompute
+ 累积历史、清洗：根据数据业务需求及稽核和审计要求保存历史数据、清洗数据


模型层次关系如下图：
![/images/docImages/design3.png](/images/docImages/design3.png)

#### 公共维度模型层（CDM）
存放 <font color="lightgreen">明细数组层(DWD) 、汇总数据层(DWS) 及 维度指标数据(DIM)</font>
明细数组层(DWD) 和 维度数据一般根据ODS层数据加工生成。
汇总数据层(DWS) 由维度数据和明细数据加工生成。

#### 应用数据层（ADS）
存储数据产品个性化的统计指标数据，根据CDM层与ODS层加工生成

### 模型建议
### 构建流程