---
title: mysql
layout: post
date: 2017-04-28 15:25:25 +0800
categories: DataBase
tags: mysql
---


* content
{:toc}                                                                                                          
本文介绍mysql










### 锁

    LOCK TABLE `table` [READ|WRITE]
解锁
    UNLOCK TABLES;
引用专业的描述是
LOCK TABLES为当前线程锁定表。 UNLOCK TABLES释放被当前线程持有的任何锁。当线程发出另外一个LOCK TABLES时，或当服务器的连接被关闭时，当前线程锁定的所有表会自动被解锁。 
如果一个线程获得在一个表上的一个READ锁，该线程和所有其他线程只能从表中读。 如果一个线程获得一个表上的一个WRITE锁，那么只有持锁的线程READ或WRITE表，其他线程被阻止。

### online-schema-change

修改线上表结构要求很高，不能影响线上数据、不能将表锁定时间过长
目前有多种方式修改线上mysql表结构，都有一定出问题的概率，本文介绍技术相对成熟的修改表结构方法
--pt







#### 问题描述

数据库为mysql，采用主从模式，分库分表结构，每个库有1200张相同表，每张表大约有数据几十万记录，新需求要向表增加字段，但是生产环境不能停，数据库不能离线，必须在系统运行的情况下更新表结构。不能直接alter表,这样会产生很长时间的锁，对业务影响很大，需要经过特殊处理。

上线需要先修改表结构，然后上业务代码。


#### refrence
 
- http://www.ywnds.com/?p=4442