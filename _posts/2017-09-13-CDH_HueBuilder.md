---
layout: post
title: 'HueBuilder'
date: 2017-09-13
author: 男孩
tags: CDH
---
#### CDH之Hue的安装并使用hive

#### 介绍：
##### 一， 要想安装Hue，必须先安装oozie
0，下载[ext-2.2.zip](http://archive.cloudera.com/gplextras/misc/ )
```css
mv ext-2.2.zip /var/lib/oozie/ 
cd /var/lib/oozie 
unzip ext-2.2.zip 
chown -R oozie:oozie ext-2.2
```
1，首先点击服务，继续
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/oozie1.png)
2，选择一个平台安装，我这块和hive 安装在一块
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/oozie2.png)
3，配置节点，这块我只放了一个hadoop01节点
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/oozie3.png)
4，配置mysql，在mysql里创建oozie数据库
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/oozie4.png)
5，完成配置就可以了
打开浏览器 http://192.168.1.23:11000/oozie/  查看是否存在
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/oozie6.png)
##### 二，安装hue
1，点击服务，然后安装
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue1.png)
2，选择主机
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue2.png)
3，数据库，在mysql里创建hue数据库
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue3.png)
<font color="red"> 这块会报一个error，收我的数据库连接不上，但是我oozie都可以，所以排除是驱动的问题，进日志观察，是什么问题
</font>
```css
less  /opt/cm-5.16.1/log/cloudera-scm-server/cloudera-scm-server.log
```
4，发现是我的 libmysqlclient_r.so.16 找不见，这个问题主要原因
```css
1,包没有下载全
2，mysql的这个文件没有进行软连接
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue3.err.jpg)
```css
解决方案：
1,进行软连接
ln /usr/local/mysql/lib/libmysqlclient.so.20 /usr/lib64/libmysqlclient_r.so.16
2，之后又报一个version的错误
yum install mysql-libs
3，错误解决，进行安装
```
5，安装完成之后呢，hue server正常启动，但是load banaer启动失败
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue4.png)
<font color="red">查看日志:
Failed to find the Apache HTTPD executable.</font>
```css
####那就安装一下httpd
yum install -y httpd
```
<font color="red">
还是出错,查看日志:
httpd: Syntax error on line 82 of /opt/cm-5.16.1/run/cloudera-scm-agent/process/197-hue-HUE_LOAD_BALANCER/httpd.conf: Cannot load /usr/lib64/httpd/modules/mod_ssl.so into server: /usr/lib64/httpd/modules/mod_ssl.so: cannot open shared object file: No such file or directory
</font>
```css
//centos自带的模块没有mod_ssl 那就装呗
yum install mod_ssl
```
6，终于就是安装好了，其实cdh出错，日志报的还是挺详细的，不要慌，遇到错误
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue_end.png)
7，最终的样子
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue7.png)
8，打开浏览器，进入hue的webUi
```css
http://hadoop01:8888/hue/accounts/login/?next=/
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue_web01.png)
9，有一个新的错误，使用hive的时候出现这个错误
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue_err_hive.png)
```css
yum install cyrus-sasl-plain  cyrus-sasl-devel  cyrus-sasl-gssapi
//刷新hue页面，还没好的情况下，执行下面这句,然后再重启集群,刷新hueweb_ui
yum install apache-maven ant asciidoc cyrus-sasl-devel cyrus-sasl-gssapi gcc gcc-c++ krb5-devel libxml2-devel libxslt-devel make mysql mysql-devel openldap-devel python-devel sqlite-devel gmp-devel
```
10，重启集群服务,正常了
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue_four.png)
11，往hive里插入语句的时候进行报错。没有权限错误
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue_hive.png)
```css
修改权限:
打开hdfs，打开配置，搜索(dfs.permissions)
去掉检查服务范围，然后重启过时服务就可以了
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue_hive_end.jpg)
12，插入查询都没有问题了
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue_insert.png)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue_select.png)
13，没有很难，hue对于hive的查询还真是有点方便呢... ...
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/CDH/hue_biao.jpg)


