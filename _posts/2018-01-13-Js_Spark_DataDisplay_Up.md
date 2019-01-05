---
layout: post
title: 'JS埋点_Spark分析_echarts展示（上）'
date: 2018-01-13
author: 男孩
tags: spark
---
#### JS埋点_Spark分析_echarts展示 上部分
##### 总体架构
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/stage.png)
##### 总体架构 就是 js 将用户访问日志不断采集到nginx，经由脚本切割日志文件，然后由flume采集到hdfs，再经过spark任务分析，将结果存储到mysql，然后由echarts框架将数据展示到前端
##### 实现JS埋点以及到nginx的数据采集
<iframe width="560" height="560" src="http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/JS.mp4" frameborder="0" allowfullscreen></iframe>
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

##### 因为找不见大量的人访问，所以需要自己写一个模拟访问器，
<iframe width="560" height="560" src="http://mgimg-ali.oss-cn-beijing.aliyuncs.com/project/spark/js%E5%9F%8B%E7%82%B9%E5%88%86%E6%9E%90/moni.mp4" frameborder="0" allowfullscreen></iframe>
##### 采用pythn代码,模拟点击
```css
from  selenium import webdriver
import random

class Start(object):
    def __init__(self):
        self.driver="/Users/zhancao/JOB/project/pythonProject/Simple/pyProject/moni/chromedriver"
    def start_go(self):
        self.click=[]
        #进行存储值
        self.click.append( "http://localhost:8080/ExceptionTracker_war/html/JAVA_SE.html")
        self.click.append( "http://localhost:8080/ExceptionTracker_war/html/JAVA_EE.html")
        self.click.append("http://localhost:8080/ExceptionTracker_war/html/BIG_DATA.html")
        self.click.append("http://localhost:8080/ExceptionTracker_war/html/C.html")
        self.click.append("http://localhost:8080/ExceptionTracker_war/html/C++.html")
        self.click.append("http://localhost:8080/ExceptionTracker_war/html/Python.html")
        self.click.append("http://localhost:8080/ExceptionTracker_war/")
        driver = webdriver.Chrome(self.driver)
        index=0
        while(True):
            random_i=int(random.random()*len(self.click))
            url=self.click[random_i]
            driver.get(url)
            print("======"+str(index)+"======="+url)
            index+=1

if __name__ == '__main__':
    start = Start()
    start.start_go()


```
###### [查看Js代码](https://github.com/czhyf/ExceptionTracker)
