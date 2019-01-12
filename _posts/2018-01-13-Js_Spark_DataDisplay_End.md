---
layout: post
title: 'JS埋点_Spark分析_echarts展示（下）'
date: 2018-01-15
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
<iframe width="560" height="400" src="http://img.wordboy.cn/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/flume_caiji.mp4" frameborder="0" allowfullscreen></iframe>
##### 既然flume已经采集到kafka了 那么接下来就开始编写sparkStreaming程序来消费数据
```css
//首先idea创建一个maven程序，然后导入依赖
        <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>0.10.0.1</version>
        </dependency>

        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-streaming_2.11</artifactId>
            <version>2.2.0</version>
        </dependency>

        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-streaming-kafka-0-10_2.11</artifactId>
            <version>2.2.0</version>
        </dependency>
```
##### 我们要写一个url解码的java类。因为收集到的信息都是经过url编码的、SCALA中是可以直接调用java代码的。
```css
package com.log;

import java.io.UnsupportedEncodingException;

public class UrlUtil {
    private final static String ENCODE = "GBK";
    /**
     * URL 解码
     */
    public static String getURLDecoderString(String str) {
        String result = "";
        if (null == str) {
            return "";
        }
        try {
            result = java.net.URLDecoder.decode(str, ENCODE);
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        return result;
    }
    /**
     * URL 转码
     */
    public static String getURLEncoderString(String str) {
        String result = "";
        if (null == str) {
            return "";
        }
        try {
            result = java.net.URLEncoder.encode(str, ENCODE);
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        return result;
    }

}
```
##### 开始写sparkStreaming+kafka 的程序（很简单）
```css

package com.log

import org.apache.kafka.common.serialization.StringDeserializer
import org.apache.log4j.{Level, Logger}
import org.apache.spark.streaming.{Seconds, StreamingContext}
import org.apache.spark.{SparkConf}
import org.apache.spark.streaming.kafka010.{ConsumerStrategies, KafkaUtils, LocationStrategies}

/**
  * 业务需求
  * 1，需要知道 哪个url最受访问者的喜欢
  * 2，统计pv
  * 3，统计uv
  */
object KafKaFlume {
  Logger.getLogger("org").setLevel(Level.WARN)
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf()
      .setAppName("context")
      .setMaster("local[*]")
    val ssc = new StreamingContext(conf,Seconds(2))
    val kafkaParams = Map[String, Object](
      "bootstrap.servers" -> "192.168.1.26:9092",
      "key.deserializer" -> classOf[StringDeserializer],
      "value.deserializer" -> classOf[StringDeserializer],
      "group.id" -> "js_1",
      "auto.offset.reset" -> "earliest",
      "enable.auto.commit" -> (false: java.lang.Boolean)
    )

    val topics = Array("js_log")
    val stream = KafkaUtils.createDirectStream[String, String](
      ssc,
      LocationStrategies.PreferConsistent,
      ConsumerStrategies.Subscribe[String, String](topics, kafkaParams)
    )

    stream.foreachRDD(log=>{
		//调用java程序对url进行解码
      log.map(x=>(UrlUtil.getURLDecoderString(x.value()))).map(x=>{
        (x.split("&")(4),1)
      }).reduceByKey(_+_).foreach(println)
    })

    ssc.start()
    ssc.awaitTermination()
  }
}
```
跑出来的大概流程就是这个样子[转码前和转码后]：

<iframe width="560" height="400" src="http://img.wordboy.cn/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/url_encoding.mp4" frameborder="0" allowfullscreen></iframe>
