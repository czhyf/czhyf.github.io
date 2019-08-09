---
layout: post
title: 'Vps 安装'
date: 2019-08-06
author: 男孩
tags: vps
---
##### Vps的安装
这几天需要查看外网的资料。stackover等资料，顺便搭了一个vps。做个记录
##### 首先购买vps服务器
https://www.onevps.com/ 可以在这购买 我买的$4 价钱一个月的。
##### 购买之后linux 的操作自己查百度，等。首先进入系统
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/vps/9580eb59f442b2ec79de41d914576c4.png)
##### 安装成功之后开始部署  Shadowsocks
```css
wget --no-check-certificate  https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks.sh
chmod +x shadowsocks.sh
./shadowsocks.sh 2>&1 | tee shadowsocks.log
```
##### 成功安装之后 会显示
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/vps/5340981f45b28734ccd9c2253943167.png)
##### 手机使用代理可以访问谷歌，速度很快,看图
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/vps/ca31dff84ef31e29cce2cd9df4665aa.jpg)
##### 当linux等安装完成之后，会有疑问，如何在windows上连接这个vpn。两种方案
###### 第一种，自己去git上下载
https://github.com/shadowsocks
###### 第二种，用我已经下载好的,如果你们网速不行，就用这个,我已经上传到我阿里云的服务器上
https://mgimg-ali.oss-cn-beijing.aliyuncs.com/vps/Shadowsocks-4.1.7.1.zip
##### 有什么疑问，发邮件就可以。
up_boy@aliyun.com

