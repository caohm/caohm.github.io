---
title: java.lang.OutOfMemory
layout: post
date:  2017-02-10 10:33:15 +0800
categories: jvm
tags: jvm OutOfMemory
---

* content
{:toc}


本文介绍











#### 分析

#### XmlConfigServiceImpl源码分析

程序启动,spring通过构造方法注入预加载配置。
startRefresh预加载配置，通过synchronized锁定单例私有成员变量保证线程安全,然后异步加载。
refreshConfigs()中死循环调用createConfigChangeRequest()创建查询所有可以是否变更,然后更新
程序运行 getConfigByTypeId（）加载未预加载的配置
第一次加载同步加载loadRemoteConfig（）通过netService请求配置中心数据，大流量过来会堵塞
configMap记录使用的配置
加载完成后receiveConfigInfo（）向本地写入备份文件
启动多线程异步根据设定时间刷新此配置

#### 预发布测试加载时间
```
一次请求
caohm========================PaymentWayServiceImpl: 28912
caohm========================PaymentWayServiceImpl: 28455
caohm========================PaymentWayServiceImpl: 28981
caohm========================PaymentWayServiceImpl: 29284
caohm========================PaymentWayServiceImpl: 29906
caohm========================PaymentWayServiceImpl: 30809
二次请求
caohm========================PaymentWayServiceImpl: 0
caohm========================PaymentWayServiceImpl: 0
caohm========================PaymentWayServiceImpl: 0
caohm========================PaymentWayServiceImpl: 0
caohm========================PaymentWayServiceImpl: 0
caohm========================PaymentWayServiceImpl: 0
caohm========================PaymentWayServiceImpl: 0
caohm========================PaymentWayServiceImpl: 0
```
#### 相关日志


```
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

```
2017-03-07 23:27:57:765 [47797522] ERROR - com.server.JSFTask - Failed to send response to 192.186.218.48:22000 -> 192.186.219.22:55183 for msg id: 17441, Cause by:
java.io.IOException: Broken pipe
at sun.nio.ch.FileDispatcher.write0(Native Method)
at sun.nio.ch.SocketDispatcher.write(SocketDispatcher.java:29)
at sun.nio.ch.IOUtil.writeFromNativeBuffer(IOUtil.java:72)
at sun.nio.ch.IOUtil.write(IOUtil.java:28)
at sun.nio.ch.SocketChannelImpl.write(SocketChannelImpl.java:334)
at io.netty.channel.socket.nio.NioSocketChannel.doWrite(NioSocketChannel.java:270)
at io.netty.channel.AbstractChannel$AbstractUnsafe.flush0(AbstractChannel.java:710)
at io.netty.channel.nio.AbstractNioChannel$AbstractNioUnsafe.flush0(AbstractNioChannel.java:303)
at io.netty.channel.AbstractChannel$AbstractUnsafe.flush(AbstractChannel.java:679)
at io.netty.channel.DefaultChannelPipeline$HeadContext.flush(DefaultChannelPipeline.java:1059)
at io.netty.channel.AbstractChannelHandlerContext.invokeFlush(AbstractChannelHandlerContext.java:688)
at io.netty.channel.AbstractChannelHandlerContext.flush(AbstractChannelHandlerContext.java:669)
at io.netty.channel.ChannelOutboundHandlerAdapter.flush(ChannelOutboundHandlerAdapter.java:115)
at io.netty.channel.AbstractChannelHandlerContext.invokeFlush(AbstractChannelHandlerContext.java:688)
at io.netty.channel.AbstractChannelHandlerContext.access$1600(AbstractChannelHandlerContext.java:32)
at io.netty.channel.AbstractChannelHandlerContext$WriteAndFlushTask.write(AbstractChannelHandlerContext.java:992)
at io.netty.channel.AbstractChannelHandlerContext$AbstractWriteTask.run(AbstractChannelHandlerContext.java:924)
at io.netty.util.concurrent.SingleThreadEventExecutor.runAllTasks(SingleThreadEventExecutor.java:380)
at io.netty.channel.nio.NioEventLoop.run(NioEventLoop.java:357)
at io.netty.util.concurrent.SingleThreadEventExecutor$2.run(SingleThreadEventExecutor.java:116)
at java.lang.Thread.run(Thread.java:662)
```



UMP Alarm Message
```
【端口监控报警】应用名：支付配送基础服务;地址
连续2次访问出现异常!最近一次异常信息：访问目标地址出现网络异常或者目标端口未打开,
报警级别【Warning】,报警时间：【2017-03-09 22:07:00】
```

#### 其他日志
```
# There is insufficient memory for the Java Runtime Environment to continue.
# Native memory allocation (malloc) failed to allocate 262160 bytes for Chunk::new
# An error report file with more information is saved as:
# /export/Domains/payship.sdk.local/server1/logs/java_error_402161.log
```













### Questions



















### Refrence
