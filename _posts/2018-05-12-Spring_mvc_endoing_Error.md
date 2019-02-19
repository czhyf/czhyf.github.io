---
layout: post
title: 'spring mvc 前台传入controller 层乱码'
date: 2018-05-12
author: 男孩
tags: java
---
##### Springmvc 乱码问题
##### 前台controller 层传入后台乱码
```css
这个问题怎么解决，网上查的千篇一律，无非就是 加过滤器，等等，但是，这些是最基本的配置，还是乱码。最后通过请求，发现，是 tomcat 问题，所以最后这么配置就ok了。
```
##### 步骤 ->  1，打开apache-tomcat-7.0.92\conf\ 下的server.xml，然后添加
```css
    <Connector port="8080" protocol="HTTP/1.1" 
               connectionTimeout="20000"
               redirectPort="8443"
			   URIEncoding="UTF-8"/>
```