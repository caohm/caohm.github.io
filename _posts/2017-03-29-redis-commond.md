---
title: redis-commond
layout: post
date: 2017-03-23 22:35:24 +0800
categories: [redis]
tags: [redis]
---


* content
{:toc}                                                                                                          
记录redis 使用命令与redis存储设计








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


