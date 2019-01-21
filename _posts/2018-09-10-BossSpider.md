---
layout: post
title: 'boss直聘爬取具体信息'
date: 2018-09-10
author: 男孩
tags: python
---
#### Python原生态代码爬取boss直聘

##### 首先看一下思路
首先通过这个页面拿取到所有的详细页面的url，需要判断是否存在下一页
![](http://img.wordboy.cn/python/boss_spider/boss1.png)
拿到所有的url之后呢，再对每一个详细页面的进行抓取重要的信息。
![](http://img.wordboy.cn/python/boss_spider/boss2.png)
但是在这个过程中间，ip有可能就会被封，所以说需要搭建代理ip池，这个我用的讯代理，做的代理池，思路是 崔庆才当年写的那本书。代理更换，我选择人为固定，这块我使用的是当使用了5次之后，就更换代理ip，当然，如果你有钱的话，你可以自己搭建一个ALS。
##### 更换代理ip的代码
![](http://img.wordboy.cn/python/boss_spider/boss3.png)
##### 这是详细页面的更换代理的方式，当然还有什么，如果超时访问异常，那我重新进行访问，重新获取代理ip,具体代码块
![](http://img.wordboy.cn/python/boss_spider/boss4.png)
##### 爬取出来的格式
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/python/boss_spider/boss5.png)
##### 最后又很闲的写一了百度爬虫，随便爬取。
1，运行程序，输入要爬取的图片名称

2，输入要保存的路径，静静等待就可以。代码都和上面都放到一起。

####[查看代码](https://github.com/czhyf/BossSpider)