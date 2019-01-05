---
layout: post
title: 'JS埋点_Spark分析_echarts展示'
date: 2018-01-13
author: 男孩
tags: spark
---
#### JS埋点_Spark分析_echarts展示 上部分
##### 总体架构
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/stage.png)
##### 实现JS埋点以及到nginx的数据采集
<iframe width="560" height="315" src="http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/JS.mp4" frameborder="0" allowfullscreen></iframe>
###### [安装nginx](http://www.wordboy.cn/2017/11/01/Nginx_Builde.html)

##### 配置nginx文件,下载[nginx.conf](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/nginx.conf)
```css
//nginx.cnf 下载已经配置好的nginx.cnf配置文件然后覆盖掉
cd nginx
mv nginx.conf  conf/
```
##### 配置nginx.conf
```css
vim nginx.conf
```
配置nginx的服务器地址
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/I1%24%7E53%5BCP%28%5BRQ%29QQK%291H%5BYJ.png)
##### 将log.gif 放入到html中
[log.gif下载](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/log.gif)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/log_up.PNG)

###### [查看Js代码](https://github.com/czhyf/ExceptionTracker)
