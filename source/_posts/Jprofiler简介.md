---
title: JProfiler简介
date: 2017-12-13 16:31:25
tags:
  - JProfiler
---

## 概述
JProfiler是用于分析J2EE软件性能瓶颈并能准确定位到Java类或者方法有效解决性能问题的主流工具，它通常需要与性能测试工具如：LoadRunner配合使用，因为往往只有当系统处于压力状态下才能反映出性能问题。
## 启动项
启动参数中增加`-agentpath:D:\jprofiler8_0_1\bin\windows-x64\jprofilerti.dll=port=8849`
## 分析内存
系统的内存消耗过多往往有以下几种原因：
* 频繁创建Java对象，如：数据库查询时，没分页，导致查出表中所有记录；
* 存在大对象，如：读取文件时，不是边读边写而是先读到一个byte数组，这样如果读取的文件时50M则仅此一项操作就会占有JVM50M内存。
* 存在内存泄漏，导致已不被使用的对象不被GC回收，从而随着系统使用时间的增长，内存不断受到积压，最终OutOfMemory。

当需要预判或者问题发生需要解决系统的内存问题时，可以借助JProfiler的Memory Views和VM Telemetry Views两种视图：
解决问题1和2，需借助Memory Views视图，如下：
![](https://i.imgur.com/waZCnIX.png)
图1主体表格的三列是解决问题的关键，可以通过如下步骤分析：
* 通过单击Size列列名可排列出最占内存的对象，查看所占内存大小，如果过大并且它对应的Instance Count比较小则说明该对象是大对象，这时可以对应Name列指示的Java类路径找到项目里的Java类进行分析，如果不合理则改之。
* 通过单击Instance Count列列名可排列出内存中实例数最多的Java对象，通过Name列指示的Java类路径逐一排查可能存在的设计问题。

注：在JProfiler的下方可以通过包路径过滤需要关注的对象，如图：
![](https://i.imgur.com/rld11Gh.png)
在图2中的ViewFilters输入框中输入“org.sotower”回车，则在列表中只有列出sotower包下的对象相关信息。

注意：在该视图下方的Recorded Objects子视图可以记录对象，从而查看类在一段时间里的总共实例数、GC数和活动数，但是在使用这些功能时会导致系统的性能严重降低，例如：没开此功能时，1000并发响应时间为1s，开此功能后500并发时响应时间就达到1s，因此只有当存在内存泄漏时才开启该功能。Recorded Objects子视图记录功能没开启前的截图如下：
![](https://i.imgur.com/LA21Umg.png)

解决问题3说明的内存溢出问题，需要同时借助Memory Views和VM Telemetry Views视图。
![](https://i.imgur.com/lpPp9Vr.png)

在VM Telemetry Views试图里，可以看出JVM的总共内存分配大小、内存占用大小和内存空闲大小以及GC后内存占用的变化。在图4中可以看出蓝色区域上线有一个陡降的坡度，这时GC后内存占用下降的结果，在分析是否存在内存泄漏问题时，该下坡具有重要意义，在用LoadRunner压力测试时，在稳压期间，良好的系统内存占用应该是比较稳定，并且GC后下坡的幅度也应该比较一致，比如：前次GC释放200M，下次GC大致也是200M，这是一般规律，具体在分析时或有不同。

当发现GC后回收的力度越来越小，则说明很有可能存在内存泄漏，这时需要开启该视图的Memory Views视图的Recorded Objects子视图，开启前的视图请见：图3，开启后视图如下：
![](https://i.imgur.com/mgfTO5f.png)

![](https://i.imgur.com/nWkJwwm.png)

如图6，在该视图里打开右键菜单，不断切换GC、Live和GC Live这三种模式，并结合项目的Java类，找出本该被回收但却没有得到回收的对象的对应Java类，从而找出问题根源。

## 分析CPU
通常一个方法的执行时间越长则占用CPU的资源则越多，在JProfiler里就是通过方法的执行时间来描述对CPU的使用情况。如图：
![](https://i.imgur.com/X8ERuKy.png)

在图7中，可以发现JProfiler以很清楚的层级结构描述出在一段时间里涉及类方法的执行时间，这些时间是从开始记录到查看该视图这段时间所发生的执行时间的合计，如此可以准确反映出真实场景下的方法执行情况。
一般是用LoadRunner压一段时间后再查看该视图，通过占用时间的不同，找出系统里最耗时的类方法进行调优解决问题。
发现执行一次请求，响应时间不能满足需求时，通过这种CPU时间占有的方式分析可优化点是一种简单而有效的方式。

## 分析线程
线程的运行情况可以直接反应出系统的瓶颈所在，对线程一般有以下三个关注点：
* Web容器的线程最大数管理，Web容器允许开启的线程数与系统要求的最大并发数有一定的对应关系，通常是Web容器运行的线程数略大于最大并发数，以Tomcat为例，在`{$tomcat}/conf/server.xml`文件的Connector选项里配置maxThreads，它的默认值时150；
* 线程阻塞
* 线程死锁

在JProfiler里有专门的线程视图：Thread Views，该视图能很好的发现并解决线程相关的大多问题。线程运行历史图：
![](https://i.imgur.com/MttrRCy.png)

如图8在Thread Views的Thread History视图里可以查看Web容器共打开的线程数以及这些线程的使用情况，在视图里红色表示阻塞的时间段，红色线条越长代表阻塞的时间越长，说明在存在阻塞情况这时切换到Current Monitor Usage或者Monitor Usage History视图，如：

![](https://i.imgur.com/t0xCzEU.png)

通过视图上方的Threshold in ms指定只显示阻塞时间超过一定时间的线程，在线程表格中选中一行，一般会在下方表格里显示出等待线程和拥有线程的执行堆栈，通过分析这些执行堆栈并在项目中找到对应的Java类方法，分析阻塞原因（往往是使用synchronized同步关键字导致阻塞，因此在用到synchronized关键字时一定要谨慎），从而解决问题。

如果系统里存在线程死锁情况，则会显示在Deadlock Detection视图：
![](https://i.imgur.com/q2gCBaI.png)

在该视图里会把死锁线程的Java对象调用关系以关系网的方式展示，并能定位到造成死锁的类方法，通过结合该图和Java代码分析解决问题。