---
author: "wangjinbao"
title: "Nginx负载均衡中4层代理和7层代理的区别"
date: 2022-01-01 00:00:01
description: "Nginx负载均衡中4层代理和7层代理的区别"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags: 
- linux
- categories:
#  -
#image: images/feature3/go.png
---


## 一、4层代理和7层代理什么意思？
这里的层是OSI 7层网络模型，OSI 模型是从上往下的，越底层越接近硬件，越往上越接近软件，这七层模型分别是：
<font color='#aed581'>物理层、数据链路层、网络层、传输层、会话层、表示层、应用层</font>。

+ 4层 是指传输层的 tcp / udp 。

+ 7层 是指应用层，通常是http 。

## 二、代理原理
4层 用的是NAT技术。NAT英文全称是“Network Address Translation”，中文意思是“网络地址转换”，请求进来的时候，nginx修改数据包里面的目标和源IP和端口，然后把数据包发向目标服务器，服务器处理完成后，nginx再做一次修改，返回给请求的客户端。

7层代理：需要读取并解析http请求内容，然后根据具体内容(url,参数，cookie,请求头)然后转发到相应的服务器，转发的过程是：建立和目标机器的连接，然后转发请求，收到响应数据在转发给请求客户端。



## 三、优缺点对比：
### 1、性能：
理论上4层要比7层快，因为7层代理需要解析数据包的具体内容，需要消耗额外的cpu。但nginx具体强大的网络并发处理能力， 对于一些慢连接，nginx可以先将网络请求数据缓冲完了一次性转发给上游server,这样对于上游网络并发处理能力弱的服务器(比如tomcat)，这样对tomcat来说就是慢连接变成快连接(nginx到tomcat基本上都是可靠内网),从而节省网络数据缓冲时间，提供并发性能。

### 2、灵活性：
  由于4层代理用的是NAT，所以nginx不知道请求的具体内容，所以nginx啥也干不了。 用7层代理，可以根据请求内容(url,参数，cookie,请求头)做很多事情，比如：

+ a:动态代理：不同的url转发到不同服务器。

+ b.风控：屏蔽外网IP请求某些敏感url；根据参数屏蔽某些刷单用户。

+ c.审计：在nginx层记录请求日志。




