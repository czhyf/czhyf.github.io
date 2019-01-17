---
layout: post
title: 'yarn的作业提交'
date: 2017-09-21
author: 男孩
tags: hadoop
---
#### yarn的作业提交一个过程

首先yarn是什么东西，在hadoop1.x的时候并没有yarn。mr即使计算框架也是资源调度框架，而复杂的框架造成资源分配不合理，经常浪费资源或者无法很好利用资源，所以在hadoop2.x之后呢，加入了一个yarn资源调度框架，它有什么好处呢?从此之后，集群的资源，不光是mr可以利用，spakr也可以利用，他就像一个管家，不管谁来，它负责分配资源，增加了集群资源利用率和解除了mr和集群的一个粘性。

##### 官方:
yarn是一个资源调度平台，负责为运算程序提供服务器运算资源，相当于一个分布式操作系统平台，而mapreduce等运算程序则相当于运行与操作系统之上的应用程序。yarn在hadoop2.x系列中被加入的资源管理器，取代hadoop1.x中的jobtracker，将资源管理与作业调度分离。
##### 客户端如何向yarn提交作业呢/?
首先来看一下yarn中的进程
###### ResourceManager:
 YARN分层结构的本质是ResourceManager。这个实体控制整个集群并管理应用程序向基础计算资源的分配。ResourceManager 将各个资源部分（计算、内存、带宽等）精心安排给基础NodeManager（YARN 的每节点代理）。ResourceManager还与 ApplicationMaster 一起分配资源，与NodeManager 一起启动和监视它们的基础应用程序。在此上下文中，ApplicationMaster 承担了以前的 TaskTracker 的一些角色，ResourceManager 承担了 JobTracker 的角色。
  1）处理客户端请求；
  2）启动或监控ApplicationMaster；
  3）监控NodeManager；
  4）资源的分配与调度
###### NodeManager:
odeManager管理一个YARN集群中的每个节点。NodeManager提供针对集群中每个节点的服务，从监督对一个容器的终生管理到监视资源和跟踪节点健康。MRv1通过插槽管理Map和Reduce任务的执行，而NodeManager 管理抽象容器，这些容器代表着可供一个特定应用程序使用的针对每个节点的资源。YARN继续使用HDFS层。它的主要 NameNode用于元数据服务，而DataNode用于分散在一个集群中的复制存储服务。
  1）单个节点上的资源管理；
  2）处理来自ResourceManager上的命令；
  3）处理来自ApplicationMaster上的命令。
###### ApplicationMaster
ApplicationMaster管理一个在YARN内运行的应用程序的每个实例。ApplicationMaster 负责协调来自 ResourceManager 的资源，并通过 NodeManager 监视容器的执行和资源使用（CPU、内存等的资源分配）。请注意，尽管目前的资源更加传统（CPU 核心、内存），但未来会带来基于手头任务的新资源类型（比如图形处理单元或专用处理设备）。从 YARN 角度讲，ApplicationMaster 是用户代码，因此存在潜在的安全问题。YARN 假设 ApplicationMaster 存在错误或者甚至是恶意的，因此将它们当作无特权的代码对待。
  1）负责数据的切分；
  2）为应用程序申请资源并分配给内部的任务；
  3）任务的监控与容错
###### Container
 对任务运行环境进行抽象，封装CPU、内存等多维度的资源以及环境变量、启动命令等任务运行相关的信息。比如内存、CPU、磁盘、网络等，当AM向RM申请资源时，RM为AM返回的资源便是用Container表示的。YARN会为每个任务分配一个Container，且该任务只能使用该Container中描述的资源。
  要使用一个YARN集群，首先需要来自包含一个应用程序的客户的请求。ResourceManager 协商一个容器的必要资源，启动一个ApplicationMaster 来表示已提交的应用程序。通过使用一个资源请求协议，ApplicationMaster协商每个节点上供应用程序使用的资源容器。执行应用程序时，ApplicationMaster 监视容器直到完成。当应用程序完成时，ApplicationMaster 从 ResourceManager 注销其容器，执行周期就完成了。
##### 了解了一下进程的作用，那我们接下来看一下这个这个作业的提交的一个流程
![](http://mgimg-ali.oss-cn-beijing.aliyuncs.com/hadoop/yarn/yarn.jpg)
###### 1）作业提交
```css
     client调用job.waitForCompletion方法，向整个集群提交MapReduce作业 (第1步) 。 新的作业ID(应用ID)由资源管理器分配(第2步). 作业的client核实作业的输出, 计算输入的split,将作业的资源(包括Jar包, 配置文件, split信息)拷贝给HDFS(第3步). 最后, 通过调用资源管理器的submitApplication()来提交作业(第4步).
```
###### 2）作业初始化
```csss
      当资源管理器收到submitApplication()的请求时, 就将该请求发给调度器(scheduler), 调度器分配container, 然后资源管理器在该container内启动应用管理器进程, 由节点管理器监控(第5a和5b步)。
MapReduce作业的应用管理器是一个主类为MRAppMaster的Java应用。其通过创造一些bookkeeping对象来监控作业的进度, 得到任务的进度和完成报告(第6步)。然后其通过分布式文件系统得到由客户端计算好的输入split(第7步)。然后为每个输入split创建一个map任务, 根据mapreduce.job.reduces创建reduce任务对象。
```
##### 3）任务分配  
```css
      如果作业很小，应用管理器会选择在其自己的JVM中运行任务。如果不是小作业, 那么应用管理器向资源管理器请求container来运行所有的map和reduce任务(第8步). 这些请求是通过心跳来传输的, 包括每个map任务的数据位置, 比如存放输入split的主机名和机架(rack). 调度器利用这些信息来调度任务, 尽量将任务分配给存储数据的节点, 或者退而分配给和存放输入split的节点相同机架的节点.
```
##### 4）任务运行
```css
       当一个任务由资源管理器的调度分配给一个container后, 应用管理器通过联系节点管理器来启动container(第9a步和9b步). 任务由一个主类为YarnChild的Java应用执行. 在运行任务之前首先本地化任务需要的资源, 比如作业配置, JAR文件, 以及分布式缓存的所有文件(第10步). 最后, 运行map或reduce任务(第11步).
  YarnChild运行在一个专用的JVM中, 但是YARN不支持JVM重用.
```
##### 5）进度和状态更新 
```css
　　YARN中的任务将其进度和状态（包括counter）返回给应用管理器，客户端每秒（通过mapreduce.client.progressmonitor.pollinterval设置）向应用管理器请求进度更新，展示给用户。
```
##### 6）作业完成
```css
        除了向应用管理器请求作业进度外，客户端每5分钟都会通过调用waitForCompletion()来检查作业是否完成。时间间隔可以通过mapreduce.client.completion. pollinterval来设置。作业完成之后, 应用管理器和container会清理工作状态, OutputCommiter的作业清理方法也会被调用。作业的信息会被作业历史服务器存储以备之后用户核查。
```
##### 5） MapReduce on YARN
```css
 1、MapReduce on TARN
  1）YARN负责资源管理和调度；
  2）ApplicationMaster负责任务管理。
  
 2、MapReduce ApplicationMaster
  1）MRAppMaster；
  2）每个MapReduce启动一个MRAppMaster；
  3）MRAppMaster负责任务切分、任务调度、任务监控和容错。
  
 3、MRAppMaster任务调度
  1）YARN将资源分配给MRAppMaster；
  2）MRAppMaster进一步将资源分配给内部任务。
  
 4、MRAppMaster容错
  1）MRAppMaster运行失败后，由YARN重新启动；
  2）任务运行失败后，由YARN重新申请资源。
```