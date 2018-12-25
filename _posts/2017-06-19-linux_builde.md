---
layout: post
title: 'Linux的安装'
date: 2017-06-19
author: 男孩
tags: linux
---
## linux系统的安装
公司之前是原生态的hadoop，但是自己写监控页面又觉得力不从心，但是CDH 感觉坑挺多，主要是内部结构源码不了解，不过还是搭建了CDH版本。在搭建的时候使用了戴尔的7台测试服务器来进行试验：
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/0.jpg)
### 接下来就开始安装系统
#### 第一步：开机 。。这里用一台机器做试验
##### 1:机子有点脏。。
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/1.jpg)
#### 2:按F11 进入bios。然后选择Hard-->再选择usb
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/2.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/3.jpg)
#### 3:接下来就进入安装的阶段了,按照图片的顺序
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/5.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/6.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/7.jpg)
#### 4:选择启动的方式，hard启动
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/8.jpg)
#### 5:选择磁盘，这块需要注意，有时候不知道哪一个，可以随便试
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/9.jpg)
如果出现了以下错误，可以继续换一个试，直到进去
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/10.jpg)
#### 6:已经进入了操作系统，开始安装,按照步骤
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/11.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/12.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/13.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/14.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/15.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/16.jpg)
#### 7:到这一步选择自定义分区，让我们更加清楚自己的情况，我这块有两块的磁盘的并没有做阵列。所以只使用一块磁盘做系统
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/17.jpg)
这块的时候，sdc是优盘，不要进行删除，其他的则选中进行删除
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/18.jpg)
删除完之后进行分区，首先分一个swap。这个空间我只分了20g按理说，应该是运行内存的2倍，但是我只是测试机，没有必要，而且内存比较大，所以就只设置了20g
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/19.jpg)
这块需要注意的是，允许的驱动器中，只需要选择你安装的那个硬盘，其他不勾选，选择固定大小。点击确定
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/20.jpg)
接下对/进行分区，直接选择剩余所有空间，点击确定就行，记得允许驱动器还是同上。
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/21.jpg)
然后将修改写入，磁盘，这个如果是windows上简单的话，比较快，我这块使用的是服务器，所以等待时间会比较长。
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/22.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/23.jpg)
完成之后，要查看分区，这块一定要注意，记得修改Bios引导程序，这块安装的系统在sda上，所以第一个引导就放在sda上。
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/24.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/25.jpg)
这块就讲sdc与sda互换位置，完成之后，单机下一步
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/26.jpg)
选择mini安装即可，点击下一步，慢慢等待，至此，linux系统安装完毕
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/28.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/29.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/30.jpg)
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/linux_kaixin.jpg)
#### 首先关闭防火墙
```css
service iptables stop
cdkconfig iptables off
```

#### 配置hosts文件
```css
vi /etc/hosts
```
```css
192.168.1.23 hadoop01
192.168.1.24 hadoop02
192.168.1.25 hadoop03
192.168.1.26 hadoop04
192.168.1.27 hadoop05
192.168.1.28 hadoop06
192.168.1.29 hadoop07
```
#### 网络配置
```css
vi /etc/sysconfig/network-scripts/ifcfg-em1
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/a6a8ccb5bf3119c47ffdb03fd677e3f.png)
#### 配置ssh
```css
//所有的节点执行
ssh localhost
ssh ~/.ssh
//主节点执行
ssh ssh-keygen -t rsa 
//连敲回车，就会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）
ssh-copy-id -i id_rsa.pub root@hadoop01
ssh-copy-id -i id_rsa.pub root@hadoop02
ssh-copy-id -i id_rsa.pub root@hadoop03
ssh-copy-id -i id_rsa.pub root@hadoop04
ssh-copy-id -i id_rsa.pub root@hadoop05
ssh-copy-id -i id_rsa.pub root@hadoop06
ssh-copy-id -i id_rsa.pub root@hadoop07
```
#### 安装java
1，下载rpm
```css
https://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-linux-x64.rpm
```
2，创建文件夹 并将rpm文件传到java文件里
```css
mkdir software
mkdir java
```
3，使用rpm安装
```css
rpm -ivh jdk-8u191-linux-x64.rpm
```
4，配置一个全局的java变量
```css
echo "JAVA_HOME=/usr/java/latest/" >> /etc/environment
```
#### 关闭SELinux（重启生效）（所有节点）
```css
vi /etc/selinux/config
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/linux_buidler/linux_se.png)

#### 设置用户最大可打开文件数，进程数，内存占用
```css
vi /etc/security/limits.conf
```
再最后加上
```css
* soft nofile 32728
* hard nofile 1024999
* soft nproc 65535
* hard nproc unlimited
* soft memlock unlimited
* hard memlock unlimited
```
#### 配置NTP时间同步服务
##### 安装ntp
```css
yum install -y ntp
```
###### 配置NTP
```css
#所有节点都操作
cp /etc/ntp.conf /etc/ntp.conf.backup
```
```css
#主节点
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap
server 127.127.1.0
fudge 127.127.1.0 stratum 10
#从节点
server master（此处为master节点的hostname）
```
```css
#开机启动
chkconfig ntpd on
#启动ntp
service ntpd start
```
#### 重启所有的机器，重启完之后进行接下来的操作
##### 设置swap空间
```css
echo "vm.swappiness = 0" >> /etc/sysctl.conf
```
##### 关闭大页面压缩
```css
echo never > /sys/kernel/mm/transparent_hugepage/enabled
echo never > /sys/kernel/mm/transparent_hugepage/defrag
```





