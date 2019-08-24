---
layout: post
title: 'ESXI6.0的安装'
date: 2019-08-24
author: 男孩
tags: vmware
---
##### 裸机安装ESXI6.0
之前一直用windows 安装wmware 安装虚拟机，但是觉得太没必要，还必须得安装操作系统，但是安装了这个不需要
安装操作系统，可以直接将整体硬件虚拟化出来，使用起来更加方便。
##### 首先下载ESXI6.0-从百度网盘下载。还有 vmware连接工具
链接：https://pan.baidu.com/s/1rrJ-iUVD3FKg5cJY3aPhxA   提取码：dnvq   //ESXI6.0.iso
链接：https://pan.baidu.com/s/16lbWMO5r-NZh8SF4hcC6JQ    提取码：32yi   //启动盘制作工具
链接：https://pan.baidu.com/s/1EoY89RnMrcZ8Dqq2uNbAGw    提取码：iec7    //vmware虚拟机工具
##### 1，下载好ESXI6.0.iso 利用启动盘制作工具制作启动盘
##### 2，开始安装，首先可能会出现 not a com32 image 错误 ，然后有个 boot  这个时候输入 install 即可解决
##### 3, 如果输入 Install 会到下面这个界面
###### 3.1 ESXi引导装入程序，VMware ESXi引导过程，在屏幕上方显示的版本号、内核、当前安装的硬件平台、主机配置（CPU、内存）
![](https://images2015.cnblogs.com/blog/986230/201704/986230-20170413184913908-443285635.png)
##### 4,之后进入VMware ESXi安装界面，在“Welcome to the VMware ESXi 6.0.0 Installation”对话框中，按回车键开始安装。
![](https://images2015.cnblogs.com/blog/986230/201704/986230-20170413182958767-1307945854.jpg)
![](https://images2015.cnblogs.com/blog/986230/201704/986230-20170413183006751-1509433113.jpg)
##### 5,这个时候会让你选择在哪个 硬盘上安装
##### 6.如果你的计算机上原来安装过ESXi，或者有以前的ESXi版本，则会弹出“ESXi and VMFS Found”的容器，提示找到一个ESXi与VMFS数据存储，你是：
更新这个ESXi、保留ESXi数据存储
还是安装新的ESXi、保留数据存储
还是安装新的ESXi、覆盖VMFS数据存储
如果你以前是ESXi 5.X，则可以选择第1项。如果你要安装全新的ESXi，并保留数据库则选择第2项。如果你这台机器是实验环境，则可以选择第3项
![](https://images2015.cnblogs.com/blog/986230/201704/986230-20170413185110080-655842803.jpg)
如果是在一台新的服务器安装，或者是在一个刚刚初始化过的硬盘上安装，则会弹出“Confirm Install”对话框，提示这个磁盘会重新分区，而该硬盘上的所有数据将会被删除。
##### 7，之后一路回车，直到设置密码（应该是最少7位数）
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/vmware/password.jpg)
##### 8,之后VMware ESXi会开始安装，并显示安装进度。
![](https://images2015.cnblogs.com/blog/986230/201704/986230-20170413185658314-1415732093.jpg)
![](https://images2015.cnblogs.com/blog/986230/201704/986230-20170413185703876-493843843.jpg)
##### 9，VMware ESXi 6安装比较快，在安装完成后，弹出“Installation Complete”对话框，如图1-13所示，按回车键将重新启动。
![](https://images2015.cnblogs.com/blog/986230/201704/986230-20170413185811376-952613074.png)
###### 10，当VMware ESXi启动成功后，在控制台窗口，可以看到当前服务器信息，显示了VMware ESXi 6当前运行服务器的CPU型号、主机内存大小与管理地址。
![](https://images2015.cnblogs.com/blog/986230/201704/986230-20170413190147064-1304037687.png)
###### 11，首先用cmd ping 一下ip 通不通 通的话 直接连就可以
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/vmware/e774d107d1aa427923751ade1a3881f.png)
###### 12，这就是连接上的样子，已经内存什么的，都显示出来了，可以直接建立虚拟机应用了
![](https://mgimg-ali.oss-cn-beijing.aliyuncs.com/vmware/b476b9447b6aa85685b9cec44bce784.png)
##### 有什么疑问，发邮件就可以。
up_boy@aliyun.com
