---
layout: post
title: 'windows之ssh_dispatch_run_fatal'
date: 2017-09-20
author: 男孩
tags: git
---
我在mac和linux试了没有发生这种情况，就出现在windows上很是无语,原因是连接不上git，加上dns解析

![](http://img.wordboy.cn/git/ssh_1.png)
打开 C:\Windows\System32\drivers\etc\hosts

```csss
192.30.255.112  github.com git 
185.31.16.184 github.global.ssl.fastly.net 
```
