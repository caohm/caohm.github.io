---
title: Spring aop
layout: post
date: 2018-06-06 17:30:18 +0800
categories: spring
tags: aop
---
* content
{:toc}
本文 spring复习笔记


spring aop 可以切入外部调内部方法，不能切入内部调用内部方法。解决办法，注入自己（self），通过self调用自己。
cglib 动态代理生产的子类不能包含父类私有方法，当调用私有方法时，对象是生成的代理类（子类），子类中成员变量为null。

### bean


- [Spring源码解析-beanfactory](http://blog.csdn.net/xiaoxufox/article/details/54601391)
- [spring的bean加载2](http://blog.csdn.net/long636/article/details/49364019)
- [spring源码深度解析(笔记四)--bean的加载](http://blog.csdn.net/ganxiaojieke/article/details/53749081)





