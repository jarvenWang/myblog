---
author: "wangjinbao"
title: "zookeeper、kafka、kafka-manager安装"
date: 2022-05-16
description: "zookeeper、kafka、kafka-manager安装"
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

## 新建Dockerfile
新建文件夹zookeeper、kafka、kafka-manager，并添加相应的Dockerfile
zookeeper目录下的Dockerfile内容如下：
```dockerfile
FROM wurstmeister/zookeeper
```

kafka目录下的Dockerfile内容如下：
```dockerfile
FROM wurstmeister/kafka
RUN apt-get update && apt-get install -y vim
```
kafka-manager目录下的Dockerfile内容如下：
```dockerfile
FROM sheepkiller/kafka-manager
RUN apt-get update && apt-get install -y vim
```

## 配置docker-compose.yml文件
内容如下：
```dockerfile
version: "3"
services:
    ...
    ...
    ...
  zookeeper:
    build: ./zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
    volumes:
     - /Users/wangdante/D/kugou/:/var/www/html/
    restart: always
    networks:
      jarven:
        ipv4_address: 172.19.0.18
  kafka:
    build: ./kafka
    container_name: kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 127.0.0.1
      KAFKA_MESSAGE_MAX_BYTES: 2000000
      KAFKA_CREATE_TOPICS: "Topic1:1:3,Topic2:1:1:compact"
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
     - /Users/wangdante/D/kugou/:/var/www/html/
    restart: always
    networks:
      jarven:
        ipv4_address: 172.19.0.16
  kafka-manager:
    build: ./kafka-manager
    container_name: kafka-manager
    ports:
      - "9020:9000"
    environment:
      ZK_HOSTS: zookeeper:2181
    volumes:
     - /Users/wangdante/D/kugou/:/var/www/html/
    restart: always
    networks:
      jarven:
        ipv4_address: 172.19.0.17
...
```

## 进入kafka容器修改配置
进入容器：
`docker-compose exec kafka /bin/bash`
修改配置：
```shell
cd /opt/kafka/config/
# ls
connect-console-sink.properties    connect-file-source.properties   consumer.properties  server.properties
connect-console-source.properties  connect-log4j.properties        kraft         tools-log4j.properties
connect-distributed.properties       connect-mirror-maker.properties  log4j.properties     trogdor.conf
connect-file-sink.properties       connect-standalone.properties    producer.properties  zookeeper.properties
```

修改zookeeper的配置文件：zookeeper.properties
```shell
#新建目录 kafkaLogs 和 zooLogs
mkdir /opt/kafka/kafkaLogs
mkdir /opt/kafka/zooLogs
cd /opt/kafka/config/
vim zookeeper.properties
#配置如下：
dataDir=/opt/kafka/zooLogs
clientPort=2182
maxClientCnxns=0
admin.enableServer=false
```

修改kafka的配置文件：server.porperties
```shell
############################# Server Basics #############################
broker.id=0 
############################# Socket Server Settings #############################
//9092:因为9093和下面冲突
listeners=PLAINTEXT://127.0.0.1:9093
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
 
socket.request.max.bytes=104857600
############################# Log Basics #############################
log.dirs=/opt/kafka/kafkaLogs
num.partitions=1
num.recovery.threads.per.data.dir=1
############################# Internal Topic Settings  #############################
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1
############################# Log Retention Policy #############################
log.retention.hours=168
log.segment.bytes=1073741824
log.retention.check.interval.ms=300000
############################# Zookeeper #############################
#zookeeper.connect=zookeeper:2181
#jarvenwang update
zookeeper.connect=127.0.0.1:2182
zookeeper.connection.timeout.ms=18000
############################# Group Coordinator Settings #############################
group.initial.rebalance.delay.ms=0
#port=9092
#jarvenwang update
port=9093
advertised.host.name=127.0.0.1
message.max.bytes=2000000
#jarvenwang add
advertised.port=9093
```

## 启动zookeeper 和 启动kafka
启动zookeeper:
```shell
cd /opt/kafka/config
zookeeper-server-start.sh zookeeper.properties &
```

启动kafka:
```shell
cd /opt/kafka/config
kafka-server-start.sh server.properties &
```

## 生产消息 和 消费消息
### 创建一个主题：
PS：如果已经创建了主题可以，直接查看:
`kafka-topics.sh --list --zookeeper 127.0.0.1:2182`
前提必须要开启zookeeper的服务==》kafka-topics.sh --create --zookeeper 127.0.0.1:2182 --replication-factor 1 --partitions 1 --topic test
```shell
kafka-topics.sh --create --zookeeper 127.0.0.1:2182 --replication-factor 1 --partitions 1 --topic test
#kafka-topics.sh --create --zookeeper 127.0.0.1:2182 --replication-factor 1 --partitions 1 --topic test2
```

### 查看创建的主题：
```shell
$ kafka-topics.sh --list --zookeeper 127.0.0.1:2182
jarvenwang
test
```

### 生产者：
```shell
$ kafka-console-producer.sh --broker-list 127.0.0.1:9093 --topic test
>第一个消息
>第二个消息
>
```
![/images/docImages/kk2.png](/images/docImages/kk2.png)
### 消费者：
```shell
$ kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9093 --topic test --from-beginning
第一个消息
第二个消息
```

![/images/docImages/kk1.png](/images/docImages/kk1.png)

### 查看版本：
```shell
ps -ef|grep '/libs/kafka.\{2,40\}.jar'
```