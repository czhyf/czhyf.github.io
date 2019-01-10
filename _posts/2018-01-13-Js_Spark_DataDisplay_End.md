---
layout: post
title: 'JS埋点_Spark分析_echarts展示（下）'
date: 2018-01-13
author: 男孩
tags: spark
---
#### flume采集（安装flume，这块就不多做赘述了,安装也只是测试，没用，提供一个[下载链接](http://archive.apache.org/dist/flume/)）
##### 首先自定义source  [sink->kafka->sparkstreaming]
```css
#kafka 创建主题
./kafka-topics.sh --create --zookeeper 192.168.1.26:2181 --replication-factor 1 --partitions 1  --topic js_log
#kafka 查看主题
./kafka-topics.sh --list --zookeeper 192.168.1.26:2181
```
###### 定义source
```css
# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1
#
# # Describe/configure the source
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir =/root/software/nginx/split_log
a1.sources.r1.fileHeader = true
#interce UUID
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = org.apache.flume.sink.solr.morphline.UUIDInterceptor$Builder
a1.sources.r1.interceptors.i1.headerName = key

# # Describe the sink
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.k1.kafka.bootstrap.servers = 192.168.1.26:9092
a1.sinks.k1.kafka.topic = js_log
a1.sinks.k1.kafka.flumeBatchSize = 20
a1.sinks.k1.kafka.producer.acks = 1


# # Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
#
# # Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```
##### 启动flume进行采集
```css
cd /root/software/flume/bin
//开启
./flume-ng agent -c ../conf/ -f ../myconf/log-kafka.conf -n a1 -Dflume.root.logger=INFO,console
//开启kafka消费
cd /root/software/kafka/bin
//开始消费
./kafka-console-consumer.sh --topic js_log --bootstrap-server hadoop04:9092 --from-beginning
```
视频：

