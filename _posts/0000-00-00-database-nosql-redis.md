---
title: redis
layout: post
date: 2017-03-23 22:35:24 +0800
categories: database redis
tags: redis
---


* content
{:toc}                                                                                                          
记录redis 使用命令与redis存储设计




### 概述

Redis是内存实现的key-value数据库，采用C语言编写，是一个单进程单线程模型，所有数据保存在redisServer结构中的db数组中。

### 数据类型

Redis使用SDS（简单动态字符串）而不使用C语言的，杜绝缓冲区溢出。

Redis的字典底层使用哈希表，键冲突的时候用单向链表，refresh的过程是个渐进式。

Redis公有字符串、列表、哈希、集合有序集合五种类型的对象，使用的引用计数实现内存回收。

### 事件

Redis是事件驱动，分为文件事件和时间事件，文件事件服务器对套接字操作的抽象，是监听网络通信，时间事件就是根据时间点来执行。

处理文件事件采用的IO多路复用，基于Reactor模式，以单线程方式运行。文件事件和时间事件的处理是同步的、有序的、原子的，不会中断，都是尽量减少阻塞时间。

### 客户端

Redis的服务端用链表来保存客户端的状态数据，新客户端会放到末尾，每个客户端有输入缓冲区用来记录命令请求，输出缓冲区用来存放命令返回值。

网络连接关闭、协议格式错误、输出缓冲区超出都会造成连接关闭。

### 服务端

服务器从启动到能够处理客户端命令需要：

1. 初始化服务状态；
2. 载入服务器配置；
3. 初始化数据结构；
4. 还原数据库状态；
5. 执行事件循环



### 常用命令 

#### APPEND
#### BITCOUNT
#### BITFIELD
#### BITOP
#### BITPOS
#### DECR
#### DECRBY
#### GET
#### GETBIT
#### GETRANGE
#### GETSET
#### INCR
#### INCRBY
#### INCRBYFLOAT
#### MGET
#### MSET
#### MSETNX
#### PSETEX
#### SET
#### SETBIT

#### SETEX key seconds value

设置key value 并给定过期事时间单位毫秒

#### SETNX key value

如果不存在，设置key value。 
SETNX is short for "SET if Not eXists".

#### SETRANGE

#### STRLEN





### 集合(Set)

#### SADD key member [member ...]

将一个或多个member元素加入到集合key当中，已经存在于集合的member元素将被忽略。
假如key不存在，则创建一个只包含member元素作成员的集合。
当key不是集合类型时，返回一个错误。

#### SREM key member [member ...]

移除集合key中的一个或多个member元素，不存在的member元素会被忽略。
当key不是集合类型，返回一个错误。
时间复杂度:
O(N)，N为给定member元素的数量。
返回值:
被成功移除的元素的数量，不包括被忽略的元素。

#### SMEMBERS key

返回集合key中的所有成员。
时间复杂度:
O(N)，N为集合的基数。
返回值:
集合中的所有成员。

#### SISMEMBER key member

判断member元素是否是集合key的成员。
时间复杂度:
O(1)
返回值:
如果member元素是集合的成员，返回1。
如果member元素不是集合的成员，或key不存在，返回0。

#### SCARD key

返回集合key的基数(集合中元素的数量)。
时间复杂度:
O(1)
返回值：
集合的基数。
当key不存在时，返回0。

#### SMOVE source destination member

将member元素从source集合移动到destination集合。
SMOVE是原子性操作。
如果source集合不存在或不包含指定的member元素，则SMOVE命令不执行任何操作，仅返回0。否则，member元素从source集合中被移除，并添加到destination集合中去。
当destination集合已经包含member元素时，SMOVE命令只是简单地将source集合中的member元素删除。
当source或destination不是集合类型时，返回一个错误。
时间复杂度:
O(1)
返回值:
如果member元素被成功移除，返回1。
如果member元素不是source集合的成员，并且没有任何操作对destination集合执行，那么返回0。

#### SPOP key

移除并返回集合中的一个随机元素。
时间复杂度:
O(1)
返回值:
被移除的随机元素。
当key不存在或key是空集时，返回nil。

#### SRANDMEMBER key

返回集合中的一个随机元素。

#### SINTER key [key ...]

返回一个集合的全部成员，该集合是所有给定集合的交集。

#### SINTERSTORE destination key [key ...]

此命令等同于SINTER，但它将结果保存到destination集合，而不是简单地返回结果集。

#### SUNION key [key ...]

返回一个集合的全部成员，该集合是所有给定集合的并集。

#### SUNIONSTORE destination key [key ...]

此命令等同于SUNION，但它将结果保存到destination集合，而不是简单地返回结果集。

#### SDIFF key [key ...]

返回一个集合的全部成员，该集合是所有给定集合的差集 。

#### SDIFFSTORE destination key [key ...]

此命令等同于SDIFF，但它将结果保存到destination集合，而不是简单地返回结果集。


