---
title: Spring quartz
layout: post
date: 2017-04-28 17:49:30 +0800
categories: spring
tags: quartz
---
* content
{:toc}
本文 spring复习笔记之quartz 











### CronTriggerBean

我这里邮件的重点是附件，所以确认附件的数据准确性很重要。所以当数据异常的时候，我们需要在任务执行的时间点之前将正在进行的任务暂停。
然而在暂停并回复任务后发现，任务还是被执行了一次，我们的需求是暂停后再恢复，任务将在下一个执行时间点才执行。
在此，发现Quartz中的CronTrigger有个属性misfireInstruction，默认的属性值是MISFIRE_INSTRUCTION_SMART_POLICY。为了满足需求，我们要把属性值改为MISFIRE_INSTRUCTION_DO_NOTHING。这样就可以了！！！
 
misfireInstruction的数据类型为int
MISFIRE_INSTRUCTION_SMART_POLICY = 0 
MISFIRE_INSTRUCTION_FIRE_ONCE_NOW = 1
MISFIRE_INSTRUCTION_DO_NOTHING = 2
 
在spring配置中，我们在cronTriggerBean中加入
```
<bean id="cronTrigger" class="org.springframework.scheduling.quartz.CronTriggerBean">  
      <property name="jobDetail" ref="buildTask" />  
      <property name="cronExpression" value="0 30 16 ? * 6" />  
      <property name="misfireInstruction" value="2"></property>  
</bean>  
```