---
title: JVM性能调优监控工具
layout: post
date:   2017-02-09 14:47:23 +0800
categories: [jvm性能调优]
tags: [jvm性能调优]
---

* content
{:toc}
本文介绍jvm 性能调优










现实企业级Java开发中，有时候我们会碰到下面这些问题： 

  * OutOfMemoryError，内存不足 
  * 内存泄露 
  * 线程死锁 
  * 锁争用（Lock Contention） 
  * Java进程消耗CPU过高 
  * ...... 

这些问题在日常开发中可能被很多人忽视（比如有的人遇到上面的问题只是重启服务器或者调大内存，而不会深究问题根源），但能够理解并解决这些问题是Java程序员进阶的必备要求。本文将对一些常用的JVM性能调优监控工具进行介绍，希望能起抛砖引玉之用。本文参考了网上很多资料，难以一一列举，在此对这些资料的作者表示感谢！关于JVM性能调优相关的资料，请参考文末。 

#### 一、javah命令(C Header and Stub File Generator)

 javah是用于根据JAVA本地方法，生成对应的c语言头文件及相应的stub文件的命令，使用比较简单，使用示例可以查看这篇文章：
 [JNI简单示例,包括C语言实现及调用](http://blog.csdn.net/fenglibing/article/details/4300381) 。

#### 二、jps命令(Java Virtual Machine Process Status Tool)

##### 1、介绍

用来查看基于HotSpot的JVM里面中，所有具有访问权限的Java进程的具体状态, 包括进程ID，进程启动的路径及启动参数等等，与unix上的ps类似，只不过jps是用来显示java进程，可以把jps理解为ps的一个子集。
使用jps时，如果没有指定hostid，它只会显示本地环境中所有的Java进程；如果指定了hostid，它就会显示指定hostid上面的java进程，不过这需要远程服务上开启了jstatd服务，可以参看前面的jstatd章节来启动jstad服务。
 
##### 2、命令格式

`jps [ options ] [ hostid ]`
 
##### 3、常用参数说明

-q 忽略输出的类名、Jar名以及传递给main方法的参数，只输出pid。
-m 输出传递给main方法的参数，如果是内嵌的JVM则输出为null。
-l 输出应用程序主类的完整包名，或者是应用程序JAR文件的完整路径。
-v 输出传给JVM的参数。
-V 输出通过标记的文件传递给JVM的参数（.hotspotrc文件，或者是通过参数-XX:Flags=<filename>指定的文件）。
-J 用于传递jvm选项到由javac调用的java加载器中，例如，“-J-Xms48m”将把启动内存设置为48M，使用-J选项可以非常方便的向基于Java的开发的底层虚拟机应用程序传递参数。
 
##### 4、服务器标识

hostid指定了目标的服务器，它的语法如下：
`[protocol:][[//]hostname][:port][/servername]`
protocol - 如果protocol及hostname都没有指定，那表示的是与当前环境相关的本地协议，如果指定了hostname却没有指定protocol，那么protocol的默认就是rmi。
hostname - 服务器的IP或者名称，没有指定则表示本机。
port - 远程rmi的端口，如果没有指定则默认为1099。
Servername - 注册到RMI注册中心中的jstatd的名称。
 
##### 5、使用示例

###### 5.1、列出本地的Java进程

不带任何参数
``` bash
fenglibin@libin:~$ jps
11644 Main
1947 
12843 Jps
```

带-v参数

``` bash
fenglibin@libin:~$ jps -v
11644 Main -agentlib:jdwp=transport=dt_socket,suspend=y,address=localhost:43467 -Dfile.encoding=GBK
1947  -Dosgi.requiredJavaVersion=1.5 -XX:MaxPermSize=256m -Xms40m -Xmx512m
12858 Jps -Denv.class.path=/home/fenglibin/java6/lib/dt.jar:/home/fenglibin/java6/lib/tools.jar::/usr/bin/libtool:/usr/bin/autoconf:/usr/local/BerkeleyDB.4.8/lib -Dapplication.home=/home/fenglibin/java6 -Xms8m
```

带-l参数

``` bash
fenglibin@libin:~$ jps -l
11644 com.alibaba.china.webww.core.Main
12870 sun.tools.jps.Jps
1947
```
 
###### 5.2、列出远程的Java进程

在jstatd章节，我们有通过：
``` bash
rmiregistry 2020&jstatd -J-Djava.security.policy=all.policy -p 2020 -n AlternateJstatdServerName
```
启动了名为AlternateJstatdServerName的jstatd服务，那么我们此时就可以通过该服务列出其有权限访问的Java进程。
``` bash
fenglibin@libin:~$ jps 10.1.1.234:2020/AlternateJstatdServerName
29556 Bootstrap
28671 WSPreLauncher
2602 RegistryImpl
18272 Test
2603 Jstatd
```

#### 三、jstack命令(Java Stack Trace)

##### 1、介绍
jstack用于打印出给定的java进程ID或core file或远程调试服务的Java堆栈信息，如果是在64位机器上，需要指定选项"-J-d64"，Windows的jstack使用方式只支持以下的这种方式：
```
jstack [-l] pid
```
如果java程序崩溃生成core文件，jstack工具可以用来获得core文件的java stack和native stack的信息，从而可以轻松地知道java程序是如何崩溃和在程序何处发生问题。另外，jstack工具还可以附属到正在运行的java程序中，看到当时运行的java程序的java stack和native stack的信息, 如果现在运行的java程序呈现hung的状态，jstack是非常有用的。

##### 2、命令格式

``` bash
jstack [ option ] pid
jstack [ option ] executable core
jstack [ option ] [server-id@]remote-hostname-or-IP
```

##### 3、常用参数说明

###### 1)、options： 

executable Java executable from which the core dump was produced.
(可能是产生core dump的java可执行程序)
core 将被打印信息的core dump文件
remote-hostname-or-IP 远程debug服务的主机名或ip
server-id 唯一id,假如一台主机上多个远程debug服务 

###### 2）、基本参数：

-F当’jstack [-l] pid’没有相应的时候强制打印栈信息
-l长列表. 打印关于锁的附加信息,例如属于java.util.concurrent的ownable synchronizers列表.
-m打印java和native c/c++框架的所有栈信息.
-h | -help打印帮助信息
pid 需要被打印配置信息的java进程id,可以用jps查询.

##### 4、使用示例


#### 四、jstat命令(Java Virtual Machine Statistics Monitoring Tool)

##### 1、介绍

Jstat用于监控基于HotSpot的JVM，对其堆的使用情况进行实时的命令行的统计，使用jstat我们可以对指定的JVM做如下监控：
- 类的加载及卸载情况
- 查看新生代、老生代及持久代的容量及使用情况
- 查看新生代、老生代及持久代的垃圾收集情况，包括垃圾回收的次数及垃圾回收所占用的时间
- 查看新生代中Eden区及Survior区中容量及分配情况等
jstat工具特别强大，它有众多的可选项，通过提供多种不同的监控维度，使我们可以从不同的维度来了解到当前JVM堆的使用情况。详细查看堆内各个部分的使用量，使用的时候必须加上待统计的Java进程号，可选的不同维度参数以及可选的统计频率参数。
它主要是用来显示GC及PermGen相关的信息，如果对GC不怎么了解，先看这篇文章：[ 非常详细GC学习笔记](http://blog.csdn.net/fenglibing/archive/2011/04/13/6321453.aspx)，否则其中即使你会使用jstat这个命令，你也看不懂它的输出。
 
##### 2、语法

`jstat [ generalOption | outputOptions vmid [interval[s|ms] [count]] ]`

**generalOption** - 单个的常用的命令行选项，如-help, -options, 或 -version。

**outputOptions** - 一个或多个输出选项，由单个的statOption选项组成，可以和-t, -h, and -J等选项配合使用。

>**statOption**：
>根据jstat统计的维度不同，可以使用如下表中的选项进行不同维度的统计，不同的操作系统支持的选项可能会不一样，可以通过-options选项，查看不同操作系统所支持选项，如：

|Option  |Displays...|
|---|---|
|class   |用于查看类加载情况的统计|
|compiler    |用于查看HotSpot中即时编译器编译情况的统计|
|gc  |用于查看JVM中堆的垃圾收集情况的统计|
|gccapacity  |用于查看新生代、老生代及持久代的存储容量情况|
|gccause |用于查看垃圾收集的统计情况（这个和-gcutil选项一样），如果有发生垃圾收集，它还会显示最后一次及当前正在发生垃圾收集的原因。|
|gcnew   |用于查看新生代垃圾收集的情况|
|gcnewcapacity   |用于查看新生代的存储容量情况|
|gcold   |用于查看老生代及持久代发生GC的情况|
|gcoldcapacity   |用于查看老生代的容量|
|gcpermcapacity  |用于查看持久代的容量|
|gcutil  |用于查看新生代、老生代及持代垃圾收集的情况|
|printcompilation    |HotSpot编译方法的统计|

**-h** n
>用于指定每隔几行就输出列头，如果不指定，默认是只在第一行出现列头。

**-J** javaOption
>用于将给定的javaOption传给java应用程序加载器，例如，“-J-Xms48m”将把启动内存设置为48M。如果想查看可以传递哪些选项到应用程序加载器中，可以相看如下的文档：
- [Linux and Solaris](http://docs.oracle.com/javase/1.5.0/docs/tooldocs/solaris/java.html)
- [Windows](http://docs.oracle.com/javase/1.5.0/docs/tooldocs/windows/java.html)

**-t** n
>用于在输出内容的第一列显示时间戳，这个时间戳代表的时JVM开始启动到现在的时间（注：在IBM JDK5中是没有这个选项的）。

**vmid** - VM的进程号，即当前运行的java进程号。

还有两个关于显示频率的选项：

**interval**
 – 间隔时间，单位可以是秒或者毫秒，通过指定s或ms确定，默认单位为毫秒。 

**count** - 打印次数，如果缺省则打印无数次。

##### 3、不同的统计维度（statOption）及输出说明

**-class** 类加载情况的统计

|列名 |说明|
|---    |---  |
|Loaded |加载了的类的数量|
|Bytes  |加载了的类的大小，单为Kb|
|Unloaded   |卸载了的类的数量|
|Bytes  |卸载了的类的大小，单为Kb|
|Time   |花在类的加载及卸载的时间|

**-compiler** HotSpot中即时编译器编译情况的统计

|列名 |说明|
|---    |---  |
|Compiled   |编译任务执行的次数|
|Failed |编译任务执行失败的次数|
|Invalid    |编译任务非法执行的次数|
|Time   |执行编译花费的时间|
|FailedType |最后一次编译失败的编译类型|
|FailedMethod   |最后一次编译失败的类名及方法名|

**-gc** JVM中堆的垃圾收集情况的统计

|列名 |说明|
|---    |---  |
|S0C    |新生代中Survivor space中S0当前容量的大小（KB）|
|S1C    |新生代中Survivor space中S1当前容量的大小（KB）|
|S0U    |新生代中Survivor space中S0容量使用的大小（KB）|
|S1U    |新生代中Survivor space中S1容量使用的大小（KB）|
|EC |Eden space当前容量的大小（KB）|
|EU |Eden space容量使用的大小（KB）|
|OC |Old space当前容量的大小（KB）|
|OU |Old space使用容量的大小（KB）|
|PC |Permanent space当前容量的大小（KB）|
|PU |Permanent space使用容量的大小（KB）|
|YGC    |从应用程序启动到采样时发生 Young GC 的次数|
|YGCT   |从应用程序启动到采样时 Young GC 所用的时间(秒)|
|FGC    |从应用程序启动到采样时发生 Full GC 的次数|
|FGCT   |从应用程序启动到采样时 Full GC 所用的时间(秒)|
|GCT    |T从应用程序启动到采样时用于垃圾回收的总时间(单位秒)，它的值等于YGC+FGC|

**-gccapacity** 新生代、老生代及持久代的存储容量情况

|列名 |说明|
|---    |---  |
|NGCMN  |新生代的最小容量大小（KB）|
|NGCMX  |新生代的最大容量大小（KB）|
|NGC    |当前新生代的容量大小（KB）|
|S0C    |当前新生代中survivor space 0的容量大小（KB）|
|S1C    |当前新生代中survivor space 1的容量大小（KB）|
|EC |Eden space当前容量的大小（KB）|
|OGCMN  |老生代的最小容量大小（KB）|
|OGCMX  |老生代的最大容量大小（KB）|
|OGC    |当前老生代的容量大小（KB）|
|OC |当前老生代的空间容量大小（KB）|
|PGCMN  |持久代的最小容量大小（KB）|
|PGCMX  |持久代的最大容量大小（KB）|
|PGC    |当前持久代的容量大小（KB）|
|PC |当前持久代的空间容量大小（KB）|
|YGC    |从应用程序启动到采样时发生 Young GC 的次数|
|FGC    |从应用程序启动到采样时发生 Full GC 的次数|

**-gccause**      这个选项用于查看垃圾收集的统计情况（这个和-gcutil选项一样），如果有发生垃圾收集，它还会显示最后一次及当前正在发生垃圾收集的原因，它比-gcutil会多出最后一次垃圾收集原因以及当前正在发生的垃圾收集的原因。
用于查看垃圾收集的统计情况，包括最近发生垃圾的原因

|列名 |说明|
|---    |---  |
|LGCC   |最后一次垃圾收集的原因，可能为“unknown GCCause”、“System.gc()”等|
|GCC    |当前垃圾收集的原因|

**-gcnew** 新生代垃圾收集的情况

|列名 |说明|
|---    |---  |
|---    |---  |
|S0C    |当前新生代中survivor space 0的容量大小（KB）|
|S1C    |当前新生代中survivor space 1的容量大小（KB）|
|S0U    |S0已经使用的大小（KB）|
|S1U    |S1已经使用的大小（KB）|
|TT |Tenuring threshold，要了解这个参数，我们需要了解一点Java内存对象的结构，在Sun JVM中，（除了数组之外的）对象都有两个机器字（words）的头部。第一个字中包含这个对象的标示哈希码以及其他一些类似锁状态和等标识信息，第二个字中包含一个指向对象的类的引用，其中第二个字节就会被垃圾收集算法使用到。在新生代中做垃圾收集的时候，每次复制一个对象后，将增加这个对象的收集计数，当一个对象在新生代中被复制了一定次数后，该算法即判定该对象是长周期的对象，把他移动到老生代，这个阈值叫着tenuring threshold。这个阈值用于表示某个/些在执行批定次数youngGC后还活着的对象，即使此时新生的的Survior没有满，也同样被认为是长周期对象，将会被移到老生代中。|
|MTT    |Maximum tenuring threshold，用于表示TT的最大值。|
|DSS    |Desired survivor size (KB).可以参与这里：[Minor GC 中 MaxTenuringThreshold 和 TargetSurvivorRatio 参数说明](http://blog.csdn.net/yangjun2/article/details/6542357)|
|EC |Eden space当前容量的大小（KB）|
|EU |Eden space已经使用的大小（KB）|
|YGC    |从应用程序启动到采样时发生 Young GC 的次数|
|YGCT   |从应用程序启动到采样时 Young GC 所用的时间(单位秒)|

**-gcnewcapacity** 新生代的存储容量情况

|列名 |说明|
|---    |---  |
|NGCMN  |新生代的最小容量大小（KB）|
|NGCMX  |新生代的最大容量大小（KB）|
|NGC    |当前新生代的容量大小（KB）|
|S0CMX  |新生代中SO的最大容量大小（KB）|
|S0C    |当前新生代中SO的容量大小（KB）|
|S1CMX  |新生代中S1的最大容量大小（KB）|
|S1C    |当前新生代中S1的容量大小（KB）|
|ECMX   |新生代中Eden的最大容量大小（KB）|
|EC |当前新生代中Eden的容量大小（KB）|
|YGC    |从应用程序启动到采样时发生 Young GC 的次数|
|FGC    |从应用程序启动到采样时发生 Full GC 的次数|

**-gcold** 老生代及持久代发生GC的情况

|列名 |说明|
|---    |---  |
|PC |当前持久代容量的大小（KB）|
|PU |持久代使用容量的大小（KB）|
|OC |当前老年代容量的大小（KB）|
|OU |老年代使用容量的大小（KB）|
|YGC    |从应用程序启动到采样时发生 Young GC 的次数|
|FGC    |从应用程序启动到采样时发生 Full GC 的次数|
|FGCT   |从应用程序启动到采样时 Full GC 所用的时间(单位秒)|
|GCT    |从应用程序启动到采样时用于垃圾回收的总时间(单位秒)，它的值等于YGC+FGC|

**-gcoldcapacity** 老生代的存储容量情况

|列名 |说明|
|---    |---  |
|OGCMN  |老生代的最小容量大小（KB）|
|OGCMX  |老生代的最大容量大小（KB）|
|OGC    |当前老生代的容量大小（KB）|
|OC |当前新生代的空间容量大小（KB）|
|YGC    |从应用程序启动到采样时发生 Young GC 的次数|
|FGC    |从应用程序启动到采样时发生 Full GC 的次数|
|FGCT   |从应用程序启动到采样时 Full GC 所用的时间(单位秒)|
|GCT    |从应用程序启动到采样时用于垃圾回收的总时间(单位秒)，它的值等于YGC+FGC|

**-gcpermcapacity**     从应用程序启动到采样时发生 Full GC 的次数| 持久代的存储容量情况

|列名 |说明|
|---    |---  |
|PGCMN  |持久代的最小容量大小（KB）|
|PGCMX  |持久代的最大容量大小（KB）|
|PGC    |当前持久代的容量大小（KB）|
|PC |当前持久代的空间容量大小（KB）|
|YGC    |从应用程序启动到采样时发生 Young GC 的次数|
|FGC    |                                         | 
|FGCT   |从应用程序启动到采样时 Full GC 所用的时间(单位秒)|
|GCT    |从应用程序启动到采样时用于垃圾回收的总时间(单位秒)，它的值等于YGC+FGC|

**-gcuti** 新生代、老生代及持代垃圾收集的情况

|列名 |说明|
|---    |---  |
|S0 |Heap上的 Survivor space 0 区已使用空间的百分比|
|S1 |Heap上的 Survivor space 1 区已使用空间的百分比|
|E  |Heap上的 Eden space 区已使用空间的百分比|
|O  |Heap上的 Old space 区已使用空间的百分比|
|P  |Perm space 区已使用空间的百分比|
|YGC    |从应用程序启动到采样时发生 Young GC 的次数|
|YGCT   |从应用程序启动到采样时 Young GC 所用的时间(单位秒)|
|FGC    |从应用程序启动到采样时发生 Full GC 的次数|
|FGCT   |从应用程序启动到采样时 Full GC 所用的时间(单位秒)|
|GCT    |从应用程序启动到采样时用于垃圾回收的总时间(单位秒)，它的值等于YGC+FGC|

**-printcompilation** HotSpot编译方法的统计

|列名 |说明|
|---    |---  |
|Compiled   |编译任务执行的次数|
|Size   |方法的字节码所占的字节数|
|Type   |编译类型|
|Method |指定确定被编译方法的类名及方法名，类名中使名“/”而不是“.”做为命名分隔符，方法名是被指定的类中的方法，这两个字段的格式是由HotSpot中的“-XX:+PrintComplation”选项确定的。|

#### 五、jmap命令(Java Memory Map)

##### 1、介绍

打印出某个java进程（使用pid）内存内的，所有‘对象’的情况（如：产生那些对象，及其数量）。
可以输出所有内存中对象的工具，甚至可以将VM 中的heap，以二进制输出成文本。使用方法 jmap -histo pid。如果连用SHELL jmap -histo pid>a.log可以将其保存到文本中去，在一段时间后，使用文本对比工具，可以对比出GC回收了哪些对象。jmap -dump:format=b,file=outfile 3024可以将3024进程的内存heap输出出来到outfile文件里，再配合MAT（内存分析工具(Memory Analysis Tool）或与jhat (Java Heap Analysis Tool)一起使用，能够以图像的形式直观的展示当前内存是否有问题。
64位机上使用需要使用如下方式：`jmap -J-d64 -heap pid`

##### 2、命令格式

```
SYNOPSIS
       jmap [ option ] pid
       jmap [ option ] executable core
       jmap [ option ] [server-id@]remote-hostname-or-IP
```

##### 3、参数说明

###### 1)、options： 

executable Java executable from which the core dump was produced.
(可能是产生core dump的java可执行程序)
core 将被打印信息的core dump文件
remote-hostname-or-IP 远程debug服务的主机名或ip
server-id 唯一id,假如一台主机上多个远程debug服务 

###### 2）、基本参数：

-dump:[live,]format=b,file=<filename>使用hprof二进制形式,输出jvm的heap内容到文件=. live子选项是可选的，假如指定live选项,那么只输出活的对象到文件. 

-finalizerinfo 打印正等候回收的对象的信息.

-heap 打印heap的概要信息，GC使用的算法，heap的配置及wise heap的使用情况.

-histo[:live] 打印每个class的实例数目,内存占用,类全名信息. VM的内部类名字开头会加上前缀”*”. 如果live子参数加上后,只统计活的对象数量. 

-permstat 打印classload和jvm heap长久层的信息. 包含每个classloader的名字,活泼性,地址,父classloader和加载的class数量. 另外,内部String的数量和占用内存数也会打印出来. 

-F 强迫.在pid没有相应的时候使用-dump或者-histo参数.在这个模式下,live子参数无效. 

-h \| -help 打印辅助信息. 

-J 传递参数给jmap启动的jvm. 

pid 需要被打印配相信息的java进程id,创业与打工的区别 - 博文预览,可以用jps查问.

##### 4、使用示例

 1)、[fenglb@ccbu-156-5 ~]$ jmap -histo 4939

 2）、[fenglb@ccbu-156-5 ~]$ jmap -dump:format=b,file=test.bin 4939

#### 六、jinfo命令(Java Configuration Info)

jinfo可以输出并修改运行时的java 进程的opts。用处比较简单，用于输出JAVA系统参数及命令行参数。用法是jinfo -opt  pid 如：查看2788的MaxPerm大小可以用  jinfo -flag MaxPermSize 2788。

#### 七、jconsole命令(Java Monitoring and Management Console)

jconsole:一个java GUI监视工具，可以以图表化的形式显示各种数据。并可通过远程连接监视远程的服务器VM。用java写的GUI程序，用来监控VM，并可监控远程的VM，非常易用，而且功能非常强。命令行里打 jconsole，选则进程就可以了。
需要注意的就是在运行jconsole之前，必须要先设置环境变量DISPLAY，否则会报错误，Linux下设置环境变量如下：
export DISPLAY=:0.0

#### 八、jvisualvm命令(Java Virtual Machine Monitoring, Troubleshooting, and Profiling Tool)

jvisualvm同jconsole都是一个基于图形化界面的、可以查看本地及远程的JAVA GUI监控工具，Jvisualvm同jconsole的使用方式一样，直接在命令行打入Jvisualvm即可启动，不过Jvisualvm相比，界面更美观一些，数据更实时

#### 九、jhat命令(Java Heap Analyse Tool)

Jhat用于对JAVA heap进行离线分析的工具，他可以对不同虚拟机中导出的heap信息文件进行分析，如Linux上导出的文件可以拿到WINDOWS上进行分析，可以查找诸如内存方面的问题，使用方式可以查看这篇文章：
http://blog.csdn.net/gtuu0123/archive/2010/11/27/6039474.aspx
不过jhat和MAT比较起来，就没有MAT那么直观了，MAT是以图形界面的方式展现结果，MAT的使用方式可以参看文章：
http://blog.csdn.net/fenglibing/archive/2011/04/02/6298326.aspx

#### 十、Jdb命令(The Java Debugger) 

用来对core文件和正在运行的Java进程进行实时地调试，里面包含了丰富的命令帮助您进行调试，它的功能和Sun studio里面所带的dbx非常相似，但 jdb是专门用来针对Java应用程序的。现在应该说日常的开发中很少用到JDB了，因为现在的IDE已经帮我们封装好了，如使用ECLIPSE调用程序就是非常方便的，只要在非常特定的情况下可能会用到这个命令，如远程服务器的维护，没有IDE进行调试，那这个时候JDB应该可以帮上忙。

----

**A、** jps(Java Virtual Machine Process Status Tool) 

jps主要用来输出JVM中运行的进程状态信息。语法格式如下： 
    
    
    jps [options] [hostid]

如果不指定hostid就默认为当前主机或服务器。 

命令行参数选项说明如下： 
    
    
    -q 不输出类名、Jar名和传入main方法的参数
    -m 输出传入main方法的参数
    -l 输出main类或Jar的全限名
    -v 输出传入JVM的参数

比如下面： 
    
    
    root@ubuntu:/# jps -m -l
    2458 org.artifactory.standalone.main.Main /usr/local/artifactory-2.2.5/etc/jetty.xml
    29920 com.sun.tools.hat.Main -port 9998 /tmp/dump.dat
    3149 org.apache.catalina.startup.Bootstrap start
    30972 sun.tools.jps.Jps -m -l
    8247 org.apache.catalina.startup.Bootstrap start
    25687 com.sun.tools.hat.Main -port 9999 dump.dat
    21711 mrf-center.jar

**B、** jstack 

jstack主要用来查看某个Java进程内的线程堆栈信息。语法格式如下： 
    
    
    jstack [option] pid
    jstack [option] executable core
    jstack [option] [server-id@]remote-hostname-or-ip

命令行参数选项说明如下： 
    
    
    -l long listings，会打印出额外的锁信息，在发生死锁时可以用jstack -l pid来观察锁持有情况
    -m mixed mode，不仅会输出Java堆栈信息，还会输出C/C++堆栈信息（比如Native方法）

jstack可以定位到线程堆栈，根据堆栈信息我们可以定位到具体代码，所以它在JVM性能调优中使用得非常多。下面我们来一个实例找出某个Java进程中最耗费CPU的Java线程并定位堆栈信息，用到的命令有ps、top、printf、jstack、grep。 

第一步先找出Java进程ID，我部署在服务器上的Java应用名称为mrf-center： 
    
    
    root@ubuntu:/# ps -ef | grep mrf-center | grep -v grep
    root     21711     1  1 14:47 pts/3    00:02:10 java -jar mrf-center.jar

得到进程ID为21711，第二步找出该进程内最耗费CPU的线程，可以使用ps -Lfp pid或者ps -mp pid -o THREAD, tid, time或者top -Hp pid，我这里用第三个，输出如下： 
<en-media height="479" width="576" type="image/png" hash="fe652fc178404cc180e741d95a6ec499"/>

TIME列就是各个Java线程耗费的CPU时间，CPU时间最长的是线程ID为21742的线程，用 
    
    
    printf "%x\n" 21742

得到21742的十六进制值为54ee，下面会用到。 

OK，下一步终于轮到jstack上场了，它用来输出进程21711的堆栈信息，然后根据线程ID的十六进制值grep，如下： 
    
    
    root@ubuntu:/# jstack 21711 | grep 54ee
    "PollIntervalRetrySchedulerThread" prio=10 tid=0x00007f950043e000 nid=0x54ee in Object.wait() [0x00007f94c6eda000]

可以看到CPU消耗在PollIntervalRetrySchedulerThread这个类的Object.wait()，我找了下我的代码，定位到下面的代码： 
    
    
    // Idle wait
    getLog().info("Thread [" + getName() + "] is idle waiting...");
    schedulerThreadState = PollTaskSchedulerThreadState.IdleWaiting;
    long now = System.currentTimeMillis();
    long waitTime = now + getIdleWaitTime();
    long timeUntilContinue = waitTime - now;
    synchronized(sigLock) {
        try {
            if(!halted.get()) {
                sigLock.wait(timeUntilContinue);
            }
        } 
        catch (InterruptedException ignore) {
        }
    }

它是轮询任务的空闲等待代码，上面的sigLock.wait(timeUntilContinue)就对应了前面的Object.wait()。 

**C、** jmap（Memory Map）和jhat（Java Heap Analysis Tool） 

jmap用来查看堆内存使用状况，一般结合jhat使用。 

jmap语法格式如下： 
    
    
    jmap [option] pid
    jmap [option] executable core
    jmap [option] [server-id@]remote-hostname-or-ip

如果运行在64位JVM上，可能需要指定-J-d64命令选项参数。 
    
    
    jmap -permstat pid

打印进程的类加载器和类加载器加载的持久代对象信息，输出：类加载器名称、对象是否存活（不可靠）、对象地址、父类加载器、已加载的类大小等信息，如下图： 
<en-media height="282" width="576" type="image/png" hash="bd67afb686c90e070173f664fcc4c2f7"/>

使用jmap -heap pid查看进程堆内存使用情况，包括使用的GC算法、堆配置参数和各代中堆内存使用情况。比如下面的例子： 
    
    
    root@ubuntu:/# jmap -heap 21711
    Attaching to process ID 21711, please wait...
    Debugger attached successfully.
    Server compiler detected.
    JVM version is 20.10-b01
    
    using thread-local object allocation.
    Parallel GC with 4 thread(s)
    
    Heap Configuration:
       MinHeapFreeRatio = 40
       MaxHeapFreeRatio = 70
       MaxHeapSize      = 2067791872 (1972.0MB)
       NewSize          = 1310720 (1.25MB)
       MaxNewSize       = 17592186044415 MB
       OldSize          = 5439488 (5.1875MB)
       NewRatio         = 2
       SurvivorRatio    = 8
       PermSize         = 21757952 (20.75MB)
       MaxPermSize      = 85983232 (82.0MB)
    
    Heap Usage:
    PS Young Generation
    Eden Space:
       capacity = 6422528 (6.125MB)
       used     = 5445552 (5.1932830810546875MB)
       free     = 976976 (0.9317169189453125MB)
       84.78829520089286% used
    From Space:
       capacity = 131072 (0.125MB)
       used     = 98304 (0.09375MB)
       free     = 32768 (0.03125MB)
       75.0% used
    To Space:
       capacity = 131072 (0.125MB)
       used     = 0 (0.0MB)
       free     = 131072 (0.125MB)
       0.0% used
    PS Old Generation
       capacity = 35258368 (33.625MB)
       used     = 4119544 (3.9287033081054688MB)
       free     = 31138824 (29.69629669189453MB)
       11.683876009235595% used
    PS Perm Generation
       capacity = 52428800 (50.0MB)
       used     = 26075168 (24.867218017578125MB)
       free     = 26353632 (25.132781982421875MB)
       49.73443603515625% used
       ....

使用jmap -histo[:live] pid查看堆内存中的对象数目、大小统计直方图，如果带上live则只统计活对象，如下： 
    
    
    root@ubuntu:/# jmap -histo:live 21711 | more
    
     num     #instances         #bytes  class name
    ----------------------------------------------
       1:         38445        5597736  <constMethodKlass>
       2:         38445        5237288  <methodKlass>
       3:          3500        3749504  <constantPoolKlass>
       4:         60858        3242600  <symbolKlass>
       5:          3500        2715264  <instanceKlassKlass>
       6:          2796        2131424  <constantPoolCacheKlass>
       7:          5543        1317400  [I
       8:         13714        1010768  [C
       9:          4752        1003344  [B
      10:          1225         639656  <methodDataKlass>
      11:         14194         454208  java.lang.String
      12:          3809         396136  java.lang.Class
      13:          4979         311952  [S
      14:          5598         287064  [[I
      15:          3028         266464  java.lang.reflect.Method
      16:           280         163520  <objArrayKlassKlass>
      17:          4355         139360  java.util.HashMap$Entry
      18:          1869         138568  [Ljava.util.HashMap$Entry;
      19:          2443          97720  java.util.LinkedHashMap$Entry
      20:          2072          82880  java.lang.ref.SoftReference
      21:          1807          71528  [Ljava.lang.Object;
      22:          2206          70592  java.lang.ref.WeakReference
      23:           934          52304  java.util.LinkedHashMap
      24:           871          48776  java.beans.MethodDescriptor
      25:          1442          46144  java.util.concurrent.ConcurrentHashMap$HashEntry
      26:           804          38592  java.util.HashMap
      27:           948          37920  java.util.concurrent.ConcurrentHashMap$Segment
      28:          1621          35696  [Ljava.lang.Class;
      29:          1313          34880  [Ljava.lang.String;
      30:          1396          33504  java.util.LinkedList$Entry
      31:           462          33264  java.lang.reflect.Field
      32:          1024          32768  java.util.Hashtable$Entry
      33:           948          31440  [Ljava.util.concurrent.ConcurrentHashMap$HashEntry;

class name是对象类型，说明如下： 
    
    
    B  byte
    C  char
    D  double
    F  float
    I  int
    J  long
    Z  boolean
    [  数组，如[I表示int[]
    [L+类名 其他对象

还有一个很常用的情况是：用jmap把进程内存使用情况dump到文件中，再用jhat分析查看。jmap进行dump命令格式如下： 
    
    
    jmap -dump:format=b,file=dumpFileName pid

我一样地对上面进程ID为21711进行Dump： 
    
    
    root@ubuntu:/# jmap -dump:format=b,file=/tmp/dump.dat 21711     
    Dumping heap to /tmp/dump.dat ...
    Heap dump file created

dump出来的文件可以用MAT、VisualVM等工具查看，这里用jhat查看： 
    
    
    root@ubuntu:/# jhat -port 9998 /tmp/dump.dat
    Reading from /tmp/dump.dat...
    Dump file created Tue Jan 28 17:46:14 CST 2014
    Snapshot read, resolving...
    Resolving 132207 objects...
    Chasing references, expect 26 dots..........................
    Eliminating duplicate references..........................
    Snapshot resolved.
    Started HTTP server on port 9998
    Server is ready.

注意如果Dump文件太大，可能需要加上-J-Xmx512m这种参数指定最大堆内存，即jhat -J-Xmx512m -port 9998 /tmp/dump.dat。然后就可以在浏览器中输入主机地址:9998查看了： 
<en-media height="637" width="576" type="image/png" hash="3a61950c3ba5b2a477ad36c142c3aec8"/>

上面红线框出来的部分大家可以自己去摸索下，最后一项支持OQL（对象查询语言）。 

**D、**jstat（JVM统计监测工具） 

语法格式如下： 
    
    
    jstat [ generalOption | outputOptions vmid [interval[s|ms] [count]] ]

vmid是Java虚拟机ID，在Linux/Unix系统上一般就是进程ID。interval是采样时间间隔。count是采样数目。比如下面输出的是GC信息，采样时间间隔为250ms，采样数为4： 
    
    
    root@ubuntu:/# jstat -gc 21711 250 4
     S0C    S1C    S0U    S1U      EC       EU        OC         OU       PC     PU    YGC     YGCT    FGC    FGCT     GCT   
    192.0  192.0   64.0   0.0    6144.0   1854.9   32000.0     4111.6   55296.0 25472.7    702    0.431   3      0.218    0.649
    192.0  192.0   64.0   0.0    6144.0   1972.2   32000.0     4111.6   55296.0 25472.7    702    0.431   3      0.218    0.649
    192.0  192.0   64.0   0.0    6144.0   1972.2   32000.0     4111.6   55296.0 25472.7    702    0.431   3      0.218    0.649
    192.0  192.0   64.0   0.0    6144.0   2109.7   32000.0     4111.6   55296.0 25472.7    702    0.431   3      0.218    0.649

要明白上面各列的意义，先看JVM堆内存布局： 
<en-media height="158" width="300" type="image/jpeg" hash="63c8a4acb68f67d8df6700f8f404b1ed"/>

可以看出： 
    
    
    堆内存 = 年轻代 + 年老代 + 永久代
    年轻代 = Eden区 + 两个Survivor区（From和To）

现在来解释各列含义： 
    
    
    S0C、S1C、S0U、S1U：Survivor 0/1区容量（Capacity）和使用量（Used）
    EC、EU：Eden区容量和使用量
    OC、OU：年老代容量和使用量
    PC、PU：永久代容量和使用量
    YGC、YGT：年轻代GC次数和GC耗时
    FGC、FGCT：Full GC次数和Full GC耗时
    GCT：GC总耗时

**E、**hprof（Heap/CPU Profiling Tool） 

hprof能够展现CPU使用率，统计堆内存使用情况。 

语法格式如下： 
    
    
    java -agentlib:hprof[=options] ToBeProfiledClass
    java -Xrunprof[:options] ToBeProfiledClass
    javac -J-agentlib:hprof[=options] ToBeProfiledClass

完整的命令选项如下： 
    
    
    Option Name and Value  Description                    Default
    ---------------------  -----------                    -------
    heap=dump|sites|all    heap profiling                 all
    cpu=samples|times|old  CPU usage                      off
    monitor=y|n            monitor contention             n
    format=a|b             text(txt) or binary output     a
    file=<file>            write data to file             java.hprof[.txt]
    net=<host>:<port>      send data over a socket        off
    depth=<size>           stack trace depth              4
    interval=<ms>          sample interval in ms          10
    cutoff=<value>         output cutoff point            0.0001
    lineno=y|n             line number in traces?         y
    thread=y|n             thread in traces?              n
    doe=y|n                dump on exit?                  y
    msa=y|n                Solaris micro state accounting n
    force=y|n              force output to <file>         y
    verbose=y|n            print messages about dumps     y

来几个官方指南上的实例。 

CPU Usage Sampling Profiling(cpu=samples)的例子： 
    
    
    java -agentlib:hprof=cpu=samples,interval=20,depth=3 Hello

上面每隔20毫秒采样CPU消耗信息，堆栈深度为3，生成的profile文件名称是java.hprof.txt，在当前目录。 

CPU Usage Times Profiling(cpu=times)的例子，它相对于CPU Usage Sampling Profile能够获得更加细粒度的CPU消耗信息，能够细到每个方法调用的开始和结束，它的实现使用了字节码注入技术（BCI）： 
    
    
    javac -J-agentlib:hprof=cpu=times Hello.java

Heap Allocation Profiling(heap=sites)的例子： 
    
    
    javac -J-agentlib:hprof=heap=sites Hello.java

Heap Dump(heap=dump)的例子，它比上面的Heap Allocation Profiling能生成更详细的Heap Dump信息： 
    
    
    javac -J-agentlib:hprof=heap=dump Hello.java

**虽然在JVM启动参数中加入-Xrunprof:heap=sites参数可以生成CPU/Heap Profile文件，但对JVM性能影响非常大，不建议在线上服务器环境使用。**

其他JVM性能调优参考资料： 

《Java虚拟机规范》 

《Java Performance》 

《Trouble Shooting Guide for JavaSE 6 with HotSpot VM》: <http://www.oracle.com/technetwork/java/javase/tsg-vm-149989.pdf>

《Effective Java》 

VisualVM: <http://docs.oracle.com/javase/7/docs/technotes/guides/visualvm/>

jConsole: <http://docs.oracle.com/javase/1.5.0/docs/guide/management/jconsole.html>

Monitoring and Managing JavaSE 6 Applications: <http://www.oracle.com/technetwork/articles/javase/monitoring-141801.html>

BTrace：<https://kenai.com/projects/btrace>

  
