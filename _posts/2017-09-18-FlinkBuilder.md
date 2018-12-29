---
layout: post
title: 'Flink的安装'
date: 2017-09-18
author: 男孩
tags: flink
---

flink的优点确实是挺强的，不过公司业务用不到，都是离线的，所以说拿出来研究研究，希望以后有机会能够用到

### Flink on  yarn 安装

##### 下载flink
```css
网址:http://mirror.bit.edu.cn/apache/flink/flink-1.7.1/flink-1.7.1-bin-hadoop27-scala_2.11.tgz
```
##### 上传然后解压
```css
//解压到自己的软件目录下
tar -zxvf flink-1.7.1-bin-hadoop27-scala_2.11.tgz
cd flink-1.7.1/
//启动yarn  前提装好yarn 因为我在测试机上装的是cdh的，所以yarn 一直运行
cd flink-1.7.1/bin
./yarn-session.sh -n 4 -jm 1024m -tm 4096m
//启动成功后
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/flink/1.PNG)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/flink/2.PNG)
```css
//提交一个任务
cd flink-1.7.0/
./bin/flink run -m yarn-cluster -yn 4 -yjm 1024m -ytm 4096m ./examples/batch/WordCount.jar
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/flink/3.PNG)
//因为基于yarn的，所以来到yarn界面查看
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/flink/4.PNG)