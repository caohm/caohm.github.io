---
title: redis standard
layout: post
date: 2017-03-23 22:35:24 +0800
categories: database redis
tags: redis
---


* content
{:toc}

redis实践





背景知识

SERVER为单线程处理模式，在处理用户请求的过程中，还会定期插入定时任务，比如:

  1）过期KEY的删除
  2）链接超时检查
  3）AOF文件重写
  4）扩容存放数据的dic容量

这些定期任务大概100ms会触发一次。当有大量的KEY同时过期时，删除过期KEY的任务可能会执行约20ms后才会退出。 大KEY(线上看到过list 的elements超过百万的)删除时会阻塞比较长的时间，具体时间见测试结果：删除命令测试数据

大KEY的危害：
  1）OPS低也会导致流量大，比如一次取走100K的数据，当OPS为1000时，就会产生100M/s的流量
  2）如果为list,hash等数据结构，大量的elements需要多次遍历，多次系统调用拷贝数据消耗时间
  3）主动删除、被动过期删除、数据迁移等，由于处理这一个KEY时间长，而发生阻塞

热KEY的危害：
  1）请求过于集中，导致单个shard压力过大，不能发挥集群多分片的优势
  2）当单个shard无法满足性能时，不能通过扩容解决



读从须知
正常情况下，主从处于增量同步状态下，延迟在ms级别，

异常情况下，当写流量大，链接断开时间长，从追不上主的增量部分，就会发生全量同步，在全量同步期间，和SLAVE拿到全量数据加载数据到内存，主从的数据可能差异比较大，甚至没有数据，这个时间长度在秒级到分钟级别不等。

当业务读从时，需要知道，当数据延迟或者没有时不会发生灾难性的后果，当然也不能因为有延迟就拒绝使用，还是要看场景，权衡好读压力和延迟哪个代价更大，JIMDB团队也会尽量保证主从数据延迟小。





最佳实践
1. 单个 key 热读导致延迟变高。

  1）增加多副本，分担读流量

  2）不要求强一致的用户，启用客户端本地缓存

  3）对于秒杀等流量突增场景，调整链接池，保持少量空闲链接

2. 单个 key 热写导致延迟变高

  1）应避免该情况发生

  2）对数据可靠性要求不高场景，建议去掉从副本，降低由于写的过快增量同步跟不上，触发全量同步，进而阻塞master的风险

3. 大 List. Set. Hash，field 数量巨大。无法横向扩容，迁移. 升级 server 会造成阻塞，获得大量元素时延迟较大阻塞其他命令。建议切割成多个小的 list. set. hash。 

4. 大 String，value 大于 20K。当 ops 为 10000，流量即为 200M,容易打满单个 server 的带宽配额. 阻塞其他命令执行。一般业务很难有效的控制value较大时key的访问频率，建议value 尽量较小。 

5. keys 命令会阻塞其他命令。建议用分时机制的 scan 命令代替。 

6. 局部读写，一段时间仅访问少量的几个分片，客户端会不停的新建. 销毁连接。建议调整 连接池配置. 增大最大空闲连接数. 空闲连接存活时长。 

7. 一次业务调用需要访问多次 JIMDB 服务端，导致 OPS 成倍增长，应尽量避免。



java sdk最佳实践

1.1 从链接池获取链接，获取链接的流程如下

  1）获取空闲链接，有则返回

  2）没有空闲链接则看池子的链接数是否大于maxTotalPerKey，没满则创建链接

  3）池子满了则看BlockWhenExhausted是怎么配置的，如果配置的是false，则请求直接报错；true则等待至多MaxWaitMillis时间，在这段时间内如果有链接空闲出来了，则返回空闲的链接

1.2 命令处理的流程如下

  1）向socket发送数据

  2）等待socket返回响应

  3）正常返回，检测当前池子的链接数是否大于maxIdlePerKey，小于则归还链接到池子，大于则销毁链接

  4）异常返回，如果是socket相关异常，销毁链接，防止链接上的数据错乱；



2.1 从超时时间的视角考虑命令处理的流程

  1）当池子里有空闲链接可用时，直接取出链接。此时的超时时间等于「等待socket返回数据的时间」

  2）当池子里没有空闲链接可用，且链接数没达到池子的最大容量时，当前线程首先会阻塞式的创建链接，然后使用创建好的链接进行命令处理。此时的超时时间等于「建立链接的超时时间」加上「等待socket返回数据的时间」

  3）当池子里没有空闲链接可用，且链接数已经达到池子的最大容量时，会导致创建链接失败，这时候会选择要不要等一等，过一会儿可能就有空闲链接释放出来。如果选择了等待，此时的超时时间等于「等待链接释放出来的超时时间」加上「等待socket返回数据的时间」



2.2 从新建链接的视角考虑整个处理的流程

  前言：

都是基于有足够量的假设，当你的ops非常小，只有1个时，这些问题也没必要去考虑了

新建链接过于频繁会导致服务端大量的时间消耗在无用的建链上，由于单线程模型，会导致命令处理延迟变大

  1）链接过于频繁的被客户端进程主动销毁。有两种情况：命令处理完毕，把链接归还到池子时，发现放不下了；池子里的链接很长时间没被使用了，池子自身做的销毁

      这就牵扯到两组参数

maxIdlePerKey（控制链接归还时要不要销毁链接）不能够设置的特别小，否则单个链接的生命周期只能做一次命令处理，虽然你的池子能创建的链接很多，但链接的利用率太低，我们知道链接的创建相对来说代价是比较高昂的

MinEvictableIdleTimeMillis(链接空闲了多少时间可以回收)同样不能设置太小，原因跟上条其实类似，生命周期太短了，不划算

  2）链接频繁的创建不成功。有两种情况：客户端进程给的超时时间太短了，还没建好就超时了；超时时间合理，但由于各种情况服务端太忙了建链接很费劲

      对于第二种情况我们暂不做讨论，因为即使链接建好了，命令处理还是有问题

      对于第一种情况，上文提到的超时时间，有些业务为了能够尽量小的控制住超时时间，把创建链接的超时设置的比较短，这就有问题了，当量特别大的时候，可能会造成大量的链接建不成功，业务命令积压或者重试，导致更多的链接需求，造成server的压立增大，建链更慢，引发雪崩

  3）流量突增导致的多个命令处理线程并发建立链接

    这种情况一般是秒杀，正常业务下业务量特别小，池子里可能都不需要维持链接，或者只维持一根链接就够用了。秒杀时刻，并行的每一个请求都会发起链接建立。

    这里就有一个参数可配置了，不需要完全根据业务量决定池子里的可用链接数，可以保持MinIdlePerKey个链接而不管这些链接是否空闲。但是这个参数也不是越大越好，每个客户端进程都会至少占用MinIdlePerKey*分片数个链接句柄，同时也会导致每个服务端至少占用client数*MinIdlePerKey个链接句柄，同时配置过大可能会导致链接频繁创建链接（原因在于定时任务会先根据过期时间决定是否逐出空闲链接，再根据该参数决定是否补齐保持的最小链接数）



2.3 再说管理端提供的配置项

  1）超时时间： 等待socket数据响应的时间

  2）新建连接超时时间： 顾名思义

  3）MaxWaitMillis： 池子里的链接达到最大数时，如果配置了要等待，等待的超时时间

  4）BlockWhenExhausted： 池子里的链接达到最大数时，要不要等待，说不准过一会儿就有链接被释放出来了

  5）MinEvictableIdleTimeMillis： 池子里的链接多长时间没被使用了，可以被销毁

  6）MinIdlePerKey： 池子保持的最小链接数量是多少，这些链接即时空闲了，也不会被上面的规则回收

  7）MaxIdlePerKey： 链接处理完命令，归还到池子里时，如果发现池子里的链接数大于这个值，则直接销毁要归还的那根链接

  8）MaxTotalPerKey： 池子的最大链接数，主要是在创建链接时，控制能不能再新建链接

  9）MaxTotal： 总的池子最大链接数

 10）读组：  可以从哪些副本上读（如果某个副本读失败，会自动尝试下一个副本，直至遍历完所有的副本）

 11）读策略：  请求怎么在读组配置的多个副本之间轮转，default:只读配置的第一组副本， randrobin:轮流读取配置的所有副本， random：在配置的副本中随机读取

参数maxtotal和maxtotalperkey的区别是：从client的视角，会和每个server维护一个链接池，所有的链接池组合成一个大池子，所有后缀是perkey控制的是到单个server的小池子，maxtotal控制的是组合的大池子

读组和读策略主要解决的是就近访问、不同客户端访问不同副本、分担读流量等问题。有一点需要注意的是，失败会挨个副本进行自动尝试，因此也会导致超时时间被放大N倍，后续会增加可配项，由用户选择是否自动重试



2.4 上续讨论都是理论层面的，如何配置，有没有公式可以套用，为什么默认配置不能搞成最优配置

    1）首先我们认为，默认配置在请求量不大、一切正常、业务没有特殊追求的情况下是够用的，当你觉得默认配置不够用时，已经不能再把jimdb当作黑盒来使用，需要对内部原理有一个了解，并且要有一个调优过程

    2）其次，没有任何配置能够适用于所有场景，所有的调优应该以实践为最终检测标准，我们只能提供一个经验值，有些可能都是拍脑门的

    3）最后，所有的值都不是越大越好



    minIdlePerKey *「client数量」最大不要超过5000（因为服务端默认的链接数上限是10000），配置为某一个值，不再频繁新建链接，意味着这个数值的链接足够应对当前业务量，再根据对未来的预期进行决策配置

    minIdlePerKey *「分片数」  该值过大的话会占用客户端过多的链接句柄

    maxIdlePerKey可以默认设成16，可以适当调大，但别超过64，更不要超过maxTotalPerKey，超过了也不会生效

    maxTotalPerKey * 「client数量」 小于 3*「单个server端最大链接数」（默认是3*10000），现在的docker越来越多了，很多情况下按照这个计算规则会不够分，因此尽量大于64吧

    新建链接的超时时间不易小于100ms

    socket的超时时间不易小于20ms（socket超时了，也会导致链接被销毁）



3.1 另外我们提供了单链接版本的客户端，延迟和吞吐损耗相比链接池版本微乎其微，提供异步和同步api，能够精准控制超时时间，有效降低链接数，如需使用可联系杨立友



c sdk最佳实践
2.1.19版本C客户端



一、           开启DEBUG日志办法

线上问题分析时，如果能够提供日志，将对解决问题带来很大的帮助。

c客户端2.1.18版本后支持通过配置开启DEBUG日志，日志级别范围0-4，数字越大打印的信息越少。配置文件放置在运行目录下，固定名称“hellojimdb.conf”，内容如：JIMDB_LOG_LEVEL 0



二、           连接池

c端程序初始化时，向CFS请求最新的配置信息，其中包括连接池相关参数。

具体参数如下：

MaxTotalPerKey： 池子的最大链接数，主要是在创建链接时，控制能不能再新建链接

MaxIdlePerKey： 链接处理完命令，归还到池子里时，如果发现池子里的链接数大于这个值，则直接销毁要归还的那根链接

MinIdlePerKey： 池子保持的最小链接数量是多少，这些链接即使空闲了，也不会被上面的规则回收

MinEvictableIdleTimeMillis： 池子里的链接多长时间没被使用了，可以被销毁

BlockWhenExhausted： 池子里的链接达到最大数时，要不要等待，说不准过一会儿就有链接被释放出来了

MaxWaitMillis： 池子里的链接达到最大数时，如果配置了要等待，等待的超时时间

连接池的初始状态是空的，受理请求时，发现池中可用连接数为0时，则会触发新建连接。新建连接时，根据最大并发连接数限制，低于则新建，否则根据是否配置BlockWhenExhausted觉得是直接返回错误还是等待；当连接使用完毕时，将放回连接到连接池，此刻会判断连接池最小空闲数，低于则直接放入池子，否则释放连接。如果高于最小空闲，但是低于最大并发，则判断当前连接的最后使用时间，如果大于连接回收时间，则释放连接，否则继续放入连接池。



三、           超时控制

C端控制超时分两部分：

1)       建连超时控制：支持参数配置

2)       命令执行超时控制：支持参数配置与接口设定

接口维度：

1)       单命令同步接口：支持参数配置

2)       Pipeline批命令异步接口：支持接口设定



建连超时时间通过参数【newTimeout】控制，参数可在管理端设定并准实时生效。命令执行超时时间，同步接口通过参数【timeout】控制，而pipeline命令接口则在接口层给予用户更大的灵活性，通过接口参数【long long ms】个性定制，但限制其不得低于20ms。此举是防止传参错误，导致极端场景下的异常阻塞。



四、           踩过的坑

超时时间得不到有效控制

服务器繁忙或网络异常时，建连、鉴权、命令执行等环节，耗时将增加。取消连接失败尝试二次连接功能，调整同步鉴权为异步鉴权模式，通过epoll调度读取鉴权结果，超时及时返回。

频繁建立连接

业务高峰期，自动扩大连接池连接数。高峰期过后，缩至参数配置值。

连接获取成功，但执行命令失败

服务器端配置5分钟空闲连接直接关闭，客户端连接池如果存在此类连接，将无法执行命令。识别此类连接，重新发起建连使其恢复正常状态。



#### 6、出现OOM command not allowed when used memory > 'maxmemory'错误是怎么回事？

需要扩容了，redis实例内存不够用了，需要进行扩容。





#### 8、java.lang.ClassFormatError:Duplicate field name&signature in class file Fastjson_ASM是什么原因？

升级fastjson版本往往可解。



#### 9、WRONGTYPE Operation against a key holding the wrong kind of value是什么原因?

字面上说的很清楚，比如你对string执行hash类型的命令，或你对list执行string的命令等等，犹如你让飞机执行游泳的命令，让鳄鱼执行飞行的命令...



#### 10、出现 invalid expire time in ...错误是什么原因？

不能在过去某时刻过期，只能在未来某时刻过期，除非你的程序具有穿越时空的功能。



#### 11、Unknow jedis exception; ... java.lang.NullPointerException是什么原因?

你检查你传入的key或value是否为null。



#### 12、Caused by: java.lang.ClassNotFoundException: com.google.common.cache.Weigher

包冲突,需要找到冲突的包,排除

例如:引入了如下包
```xml
       <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
        </dependency>
```
