---
layout: post
title: 'CDH的安装'
date: 2017-08-29
author: 男孩
tags: CDH
---
### CDH的安装
#### 啥也别说了，开始
1，[安装linux](http://www.wordboy.cn/2017/06/19/linux_builde.html)
2，[安装mysql](http://www.wordboy.cn/2017/06/19/mysql_builde.html)
#### 安装包的下载
##### Cloudera Manager下载地址：
```css
http://archive.cloudera.com/cm5/cm/5/cloudera-manager-centos6-cm5.16.1_x86_64.tar.gz
```
##### CDH安装包地址
```css
http://archive.cloudera.com/cdh5/parcels/latest/
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/cdh_choose.png)
#### 数据库的创建
```css
mysql -u root -p123456
//创建数据库

//hive数据库 
create database hive DEFAULT CHARSET utf8 COLLATE utf8_general_ci ;
//集群监控数据库
create database amon DEFAULT CHARSET utf8 COLLATE utf8_general_ci ;
//hue数据库
create database hue DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
//oozie数据库
create database oozie DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
```
#### 安装Cloudera Manager Server和 Agent
##### Master(主节点的操作)
将下载好的Cloudera Manager 压缩包上传到 /opt下面，然后解压。
```css
cd /opt
tar -zxvf cloudera-manager-centos6-cm5.16.1_x86_64.tar.gz
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/CDh1.png)
接下来下载mysql的驱动包放入到/opt/cm-5.16.1/share/cmf/lib/
```css
网址:http://dev.mysql.com/downloads/connector/j/
//下载好解压
tar -zxvf mysql-conn*.tar.gz
//移动jar包到文件下
mv ysql-connector-java-5.1.**-bin.jar /opt/cm-5.12.1/share/cmf/lib/
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/cdh_conn.png)
初始化cm数据

```css
/opt/cm-5.16.1/share/cmf/schema/scm_prepare_database.sh mysql cm -hlocalhost -uroot -p123456 --scm-host localhost scm scm scm
```
这就是初始化成功，如果遇到什么错误，查找就行，mysql的版本5.7.X 既可以
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/cdh_init.PNG)
然后配置主节点
```css
//打开config.ini
vim /opt/cm-5.16.1/etc/cloudera-scm-agent/config.ini
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/cdh_3.png)
接下来把agent分发到各个节点
```css
cd /opt
scp -r cm-5.16.1 root@hadoop02:/opt
......
scp -r cm-5.16.1 root@hadoop07:/opt
```
##### 所有的节点创建cloudera-scm用户
```css
useradd --system --home=/opt/cm-5.16.1/run/cloudera-scm-server/ --no-create-home --shell=/bin/false --comment "Cloudera SCM User" cloudera-scm
```
##### Master节点
将下载好的安装包放到    /opt/cloudera/parcel-repo/
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/CDH_parsles.PNG)
```css
//将CDH-5.12.1-1.cdh5.12.1.p0.3-el6.parcel.sha1 去掉后面的1,一定要去掉
mv CDH-5.12.1-1.cdh5.12.1.p0.3-el6.parcel.sha1 CDH-5.12.1-1.cdh5.12.1.p0.3-el6.parcel.sha
```

#### 开始启动
##### master节点，启动服务端
```css
/opt/cm-5.16.1/etc/init.d/cloudera-scm-server start
```
##### 所有节点，启动agent节点
```css
/opt/cm-5.16.1/etc/init.d/cloudera-scm-agent start
```
jps一下，查看是否有main进程，存在，如果存在，则证明启动成功,那就等个几分钟，让系统自己启动一下子。
```css
查看7180端口是否被占用,如果被占用，则证明已经启动成功了
netstat -apn|grep 7180
```
打开http://masterip:7180 默认账号：admin 密码：admin
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/0.png)
勾选是（你不勾选也不行啊，不知道这个存在的意义）
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/11754794-44a91b88cb611867.png)
选择免费的版本
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/11754794-e87db09e74a29e39.png)
点击继续
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/11754794-fd9699a955936bb6.png)
选择当前的管理的主机，点击继续
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/3.PNG)
点击下一步，然后分配，激活，都是自动的。然后是服务器检查
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/11754794-7246b5379592248b.png)
点击完成，接下来安装服务就可以了。在安装的时候发生几个错误
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/CDH_err.jpg)
最后根据日志发现是per的错误，
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/CDH_per_err.jpg)
最后安装就解决了
```css
yum -y install perl perl-devel
```
##### 安装hive的时候出现一个问题
```css
org.apache.hadoop.hive.metastore.HiveMetaException: Failed to load driver
```
是因为mysql-connect的原因
```css
将 mysql-connector-java-5.1.x-bin.jar 放到 /opt/cloudera/parcels/CDH-5.16.1-1.cdh5.16.1.p0.3/lib/hive/li
解决
```
最后想安装什么自己决定，我这块安装的了这些服务
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/CDH-end.png)