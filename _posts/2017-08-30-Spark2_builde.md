---
layout: post
title: 'CDH之spark2的安装'
date: 2017-08-29
author: 男孩
tags: CDH
---
#### CDH之spark2的安装

就之前我是cdh-5.16.1，那我们添加服务来看一下有没有spark2的安装
![](http://img.wordboy.cn/CDH/spark1.png)
没有吧，那我接下就开始让他出现吧
#### spark2的安装
##### spark2的安装包的下载 
1，首先下载 on_yarn的jar包(因为cdh的spark2目前只支持on yarn 模式)
```css
http://archive.cloudera.com/spark2/csd/
```
![](http://img.wordboy.cn/CDH/spark2.png)
2，下载spark2的安装包
```css
http://archive.cloudera.com/spark2/parcels/2.2.0/
```
![](http://img.wordboy.cn/CDH/spark3.png)
##### 安装步骤
###### 1，需要将jar包拷贝csd目录下，并改权限
```css
mv SPARK2_ON_YARN-2.2.0.cloudera3.jar /opt/cloudera/csd

chown cloudera-scm:cloudera-scm SPARK2_ON_YARN-2.2.0.cloudera3.jar
```
###### 2，上传parcel的3个包到CM的/opt/cloudera/parcel-repo目录下
如果之前有manifest.json 先把以前的重新命名,或者删除，再放spark的。
```css
SPARK2-2.2.0.cloudera4-1.cdh5.13.3.p0.603055-el6.parcel
SPARK2-2.2.0.cloudera4-1.cdh5.13.3.p0.603055-el6.parcel.sha
manifest.json
```
###### 3，重启CM和集群
```css
//主节点
/opt/cm-5.16.1/etc/init.d/cloudera-scm-server restart
//所有节点
/opt/cm-5.16.1/etc/init.d/cloudera-scm-agent restart
```
###### 4，通过cm来安装spark2
CM页面-> 主机 -> Parcel页面可以看到新的spark2的parcel包 就可以看到 spark2.2.0.cloudera4-1.cdh5.13.3.p0.603055
点击下载->分配->激活
![](http://img.wordboy.cn/CDH/spark4.png)
###### 5，点击群集 添加服务 ,就可以看见spark2了
![](http://img.wordboy.cn/CDH/spark5.png)
###### 6，完成
![](http://img.wordboy.cn/CDH/CDH-end.png)



