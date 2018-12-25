---
layout: post
title: 'Mysql的安装'
date: 2017-06-19
author: 男孩
tags: mysql
---
### Mysql二进制的安装
#### 第一步查看是否安装mysql
```css
rpm -qa|grep -i mysql
```
可能会出现一个或者多个。不管出现多少个，都全部干掉
```css
[root@localhost:~]mysql-libs-5.1.71-1.el6.x86_64
```
卸载命令：rpm –ev {包名}
```css
rpm -ev mysql-libs-5.1.71-1.el6.x86_64
```
删除完之后，继续查找是否还有mysql的目录存在
```css
find / -name mysql
```
找到之后，然后删除就可以了，利用 
```css
rm -rf 文件路径
```
最后再通过命令查一遍是否还有留下来的
```css
rpm -qa|grep -i mysql
```
##### 如果是centos7，则会默认自带安装一个mysql的分支，mariadb，必须先卸载此分支产品
```css
rpm -qa | grep mariadb
#查出之后利用rpm进行删除
rpm -e --nodeps 查找出来的文件名
```
#### 第二步 开始安装mysql
#####   yum install -y libaio &nbsp;(已经安装的可以略)
0,创建所需要的文件夹
```css
mkdir /root/software 
#创建一个文件夹
cd/root/software 
#进入文件夹
```
1,安装wget(已安装略)
```css
yum install -y wget
```
2,下载mysql的安装包，下载通用版即可。（https://dev.mysql.com/downloads/mysql/5.7.html#downloads）
```css
#通过wget下载，当然也可以手动下载，然后上传到服务器上
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
#解压到当前目录
tar -zxvf  mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz 
#将解压好的文件移动 /user/local 目录下并且重命名
mv mysql-5.7.24-linux-glibc2.12-x86_64 /usr/local/mysql
```
3,开始对mysql进行配置
```css
#进入到mysql的目录下
cd /usr/local/mysql
# 创建data文件夹，用来存储mysql的data
mkdir data
```
4,查看用户和组并创建
```css
cat /etc/group | grep mysql
cat /etc/passwd |grep mysql
#如果存在，则删除掉
userdel -r mysql #会删除其对应的组和用户。
```
```css
#创建用户组
groupadd mysql
useradd -r -g mysql mysql
#对文件赋权限
chown -R mysql:mysql /usr/local/mysql
```
5,开始创建mysql所需的目录和对mysql的配置
&nbsp;&nbsp;5.1 配置mysql.cnf
```css
vim /etc/mysql.cnf
```
```css
[mysqld]
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
port = 3306
socket=/tmp/mysql.sock

symbolic-links=0
log-error=/var/log/mysqld.log
pid-file=/tmp/mysqld/mysqld.pid
sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
log-bin=mysql-bin 
binlog-format=ROW 
server_id=1 
max_connections=1000

init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
```
&nbsp;&nbsp;5.2  创建文件 /tmp/mysql.sock：设置用户组及用户，授权
```css
cd /tmp
touch mysql.sock
chown mysql:mysql mysql.sock
chmod 755 mysql.sock
```
&nbsp;&nbsp;5.3 创建文件/tmp/mysqld/mysqld.pid
```css
cd /tmp
mkdir mysqld
cd mysqld
touch mysqld.pid
cd ..
chown -R mysql:mysql mysqld
cd mysqld
chmod 755 mysqld.pid
```
&nbsp;&nbsp;5.4 创建文件/var/log/mysqld.log
```css
touch /var/log/mysqld.log
chown -R mysql:mysql /var/log
cd log
chmod 755 mysqld.log
```
6,安装和初始化（比较重要，经常凉凉）
```css
#进入bin目录
cd /usr/local/mysql/bin/
#初始化数据库
./mysqld --initialize --user=mysql --basedir=/usr/local/mysql--datadir=/usr/local/mysql/data
#安全启动(这块有可能卡住，等一会，直接回车就行)
./mysqld_safe --user=mysql &
#是否启动成功，可以通过查看mysql进程
ps -ef | grep mysql
#拿到默认密码，在/var/log/mysqld.log里，如下：
cat /var/log/mysqld.log
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/mysql/mysql_password.png)
```css
#进入bin目录
cd /usr/local/mysql/bin
#登录mysql,输入默认密码
./mysql -u root -p
#登入进去之后，需要重新设置密码
set password=password("123456");
#设置远程登录权限
grant all privileges on *.* to 'root'@'%' identified by '123456';
#立即生效
flush privileges;
#然后退出mysql
quit;
#重新启动mysql服务
service mysql stop
service mysql start
```
#### 第三步 开机设置等操作
```css
#先拷贝
cp -a /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
#查看是否拷贝成功
cd /etc/init.d/mysql
#添加开机自启
chkconfig --add mysql
#查看是否在服务中
chkconfig --list mysql
至此就已经安装成功了.
```
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/mysql/mysql_end.jpg)