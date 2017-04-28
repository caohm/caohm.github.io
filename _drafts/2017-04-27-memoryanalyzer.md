---
title: MemoryAnalyzer
layout: post
date: 2017-04-27 04:49:12 +0800
categories: [MemoryAnalyzer]
tags: [MemoryAnalyzer]
---


* content
{:toc}                                                                                                          
本文记录MemoryAnalyzer使用及内存分析








#### java heap dump log

```
java.lang.OutOfMemoryError: Java heap space
Dumping heap to /export/Domains/[www.domain.com]/server1/logs/java_pid97379.hprof ...
Heap dump file created [3554882867 bytes in 25.471 secs]
```

#### Histogram

Lists number of instances per class

```
Class Name                                                                        | Shallow Heap | Retained Heap | Percentage
------------------------------------------------------------------------------------------------------------------------------
                                                                                  |              |               |           
org.apache.catalina.loader.WebappClassLoader @ 0x70e429b88                        |          176 |     6,922,816 |      0.21%
java.lang.Thread @ 0x700e262a0  http-1601-60 Thread                               |          112 |     2,282,432 |      0.07%
java.lang.Thread @ 0x70076e738  http-1601-83 Thread                               |          112 |     2,281,984 |      0.07%
java.lang.Thread @ 0x7039e39f0  http-1601-187 Thread                              |          112 |     2,281,936 |      0.07%
java.lang.Thread @ 0x7051a2b10  http-1601-137 Thread                              |          112 |     2,281,936 |      0.07%
java.lang.Thread @ 0x70b70add8  http-1601-158 Thread                              |          112 |     2,281,936 |      0.07%
java.lang.Thread @ 0x71a2923e0  http-1601-194 Thread                              |          112 |     2,281,936 |      0.07%
java.lang.Thread @ 0x71a29d878  http-1601-150 Thread                              |          112 |     2,281,936 |      0.07%
java.lang.Thread @ 0x71a2a2228  http-1601-131 Thread                              |          112 |     2,281,936 |      0.07%
java.lang.Thread @ 0x71a2a55d8  http-1601-116 Thread                              |          112 |     2,281,936 |      0.07%
java.lang.Thread @ 0x7005adba8  http-1601-89 Thread                               |          112 |     2,281,928 |      0.07%
java.lang.Thread @ 0x700fa0b00  http-1601-56 Thread                               |          112 |     2,281,928 |      0.07%
Total: 255 of 1,314,171 entries; 1,313,916 more                                   |              |               |           
------------------------------------------------------------------------------------------------------------------------------
```

####  Dominator Tree

List the biggest Objects and what they keep alive.

```
Class Name                                                                        | Shallow Heap | Retained Heap | Percentage
------------------------------------------------------------------------------------------------------------------------------
                                                                                  |              |               |           
org.apache.catalina.loader.WebappClassLoader @ 0x70e429b88                        |          176 |     6,922,816 |      0.21%
java.lang.Thread @ 0x700e262a0  http-1601-60 Thread                               |          112 |     2,282,432 |      0.07%
java.lang.Thread @ 0x70076e738  http-1601-83 Thread                               |          112 |     2,281,984 |      0.07%
java.lang.Thread @ 0x7039e39f0  http-1601-187 Thread                              |          112 |     2,281,936 |      0.07%
java.lang.Thread @ 0x7051a2b10  http-1601-137 Thread                              |          112 |     2,281,936 |      0.07%
Total: 255 of 1,314,171 entries; 1,313,916 more                                   |              |               |           
------------------------------------------------------------------------------------------------------------------------------
```



#### Leak Suspects

includes leak suspects and a system overview

##### System Overview

###### System Properties 

###### Thread Overview 

###### Top Consumers 

###### Class Histogram 



