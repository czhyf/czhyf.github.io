---
layout: post
title: 'Nginx的安装'
date: 2017-11-01
author: 男孩
tags: linux
---
#### nginx的安装

##### Nginx的简介
Nginx（发音同 engine x）是一款轻量级的Web 服务器／反向代理服务器及电子邮件（IMAP/POP3）代理服务器，并在一个BSD-like 协议下发行。由俄罗斯的程序设计师Igor Sysoev所开发，最初供俄国大型的入口网站及搜寻引擎Rambler（俄文：Рамблер）使用。 其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页伺服器中表现较好.目前中国大陆使用nginx网站用户有：新浪、网易、 腾讯,另外知名的微网志Plurk也使用nginx。
#####  解压nginx.tar
[1，下载nginx的压缩包](http://img.wordboy.cn/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/nginx-1.11.11.tar.gz)
2，上传并解压
```css
mv nginx-1.11.11.tar.gz 自定义文件夹
tar -zxvf nginx-1.11.11.tar.gz
```
3，安装依赖(C++)
```css
yum -y install gcc pcre-devel openssl openssl-devel
```
4，创建安装的文件夹，平检查
```css
#创建安装文件夹
mkdir nginx
#进入文件夹
cd nginx
#--prefix=创建的安装文件夹路径
./configure --prefix=/root/software/nginx
#进入解压后的文件夹
cd /root/software/nginx-1.11.11
#进行安装
make && make install
#进入安装文件夹
cd nginx
```
##### 查看是否安装成功
![](http://img.wordboy.cn/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/end.PNG)
##### 接下来启动nginx
```css
#必须用root权限
cd /root/software/nginx
#启动
sbin/nginx
```
#### 查看是否启动成功
![](http://img.wordboy.cn/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/netstat.png)
#####80端口已经启动