---
title: technology reform 
layout: post
date: 2017-02-10 10:33:15 +0800
categories: [公司]
tags: [技改]
---


* content
{:toc}
技改记录











### payship 修改的配置
- 增加 properties/monitorconfig.properties配置文件
``` bash
#umpKey prefix
monitor.cache.appkey=payship.sdk
# ump 中应用名称
countexecutor.appNameInUMP=payship.sdk.jd.com
# 系统渠道(pc、phone、qTrade、seckill、hk、jos).机房(lf、mjq、yfb)
countexecutor.originRoom=pc.yfb
```
- 删除 payship-sdk-same.properties 配置
``` bash
performanceAppKey=payship-sdk
performanceGroup=YZ
```
- PropertyPlaceholderConfigurer 增加 monitorconfig.properties
``` xml
    <value>classpath:properties/monitorconfig.properties</value>
```
-  spring/spring-config-advice.xml 修改aop切入ump监控
``` xml
    <aop:config>
        <aop:aspect id="daoAspect" ref="umpAroundAdvice">
            <aop:pointcut id="daoPointcutAop" 
                expression="execution(* com.jd.payship.sdk.dao.area.domain..*.*(..)) 
                || execution(* com.jd.payship.sdk.dao.shipment.domain..*.*(..)))"/>
            <aop:around pointcut-ref="daoPointcutAop" method="doDaoAround"/>
        </aop:aspect>
        <aop:aspect id="exportAspect" ref="umpAroundAdvice">
            <aop:pointcut id="exportPointcut" 
                expression="execution(* com.jd.payship.sdk.service..*.*(..)) "/>
            <aop:around pointcut-ref="exportPointcut" method="doExportAround"/>
        </aop:aspect>
    </aop:config>

    <!--ump-->
    <bean id="umpAroundAdvice" class="com.jd.payship.sdk.common.utils.UMPMonitorAdvice"/>
```

### trade.sdk 修改的配置
-  properties/monitorconfig.properties增加ump配置
``` bash
# ump 中应用名称
countexecutor.appNameInUMP=trade.sdk.jd.com-new
# 系统渠道(pc、phone、qTrade、seckill、hk、jos).机房(lf、mjq、yfb)
countexecutor.originRoom=pc.yfb
```
- PropertyPlaceholderConfigurer 增加 monitorconfig.properties
``` xml
    <value>classpath:properties/monitorconfig.properties</value>
```
-  spring/order-aop.xml 修改aop切入ump监控
``` xml
    <aop:config>
        <aop:aspect id="daoAspect" ref="umpAroundAdvice">
            <aop:pointcut id="daoPointcutAop" 
                expression="execution(* com.jd.trade.sdk.dao..*.*(..))"/>
            <aop:around pointcut-ref="daoPointcutAop" method="doDaoAround"/>
        </aop:aspect>
        <aop:aspect id="businessAspect" ref="umpAroundAdvice">
            <aop:pointcut id="businessPointcut" 
                expression="execution(* com.jd.trade.sdk.business..*.*(..)) "/>
            <aop:around pointcut-ref="businessPointcut" method="doExportAround"/>
        </aop:aspect>
        <aop:aspect id="exportAspect" ref="umpAroundAdvice">
            <aop:pointcut id="exportPointcut" 
                expression="execution(* com.jd.trade.sdk.service..*.*(..)) "/>
            <aop:around pointcut-ref="exportPointcut" method="doExportAround"/>
        </aop:aspect>
        <!--log-->
        <aop:aspect id="logAroundAspect" ref="rpcLogAspectUtils">
            <aop:pointcut id="logBusiness" 
                expression="execution(* com.jd.trade.sdk.service.*.*(..)) 
                || execution(* com.jd.trade.sdk.rpc.impl.*.*(..))"/>
            <aop:around pointcut-ref="logBusiness" method="doAround"/>
        </aop:aspect>
    </aop:config>

    <bean class="com.jd.ump.annotation.JAnnotation">
        <property name="jvmKey" value="web.trade.sdk.jvm"></property>
        <property name="systemKey" value="web.trade.sdk.system"></property>
    </bean>

    <!--log-->
    <bean id="rpcLogAspectUtils" class="com.jd.trade.sdk.common.utils.RpcLogAspectUtils"/>
    <!--ump-->
    <bean id="umpAroundAdvice" class="com.jd.trade.sdk.common.utils.UMPMonitorAdvice"/>
```
