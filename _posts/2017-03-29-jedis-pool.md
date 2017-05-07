---
title: redis连接池
layout: post
date: 2017-03-23 22:35:24 +0800
categories: [redis]
tags: [pool]
---


* content
{:toc}                                                                                                          
Jedis作为redis的最佳客户端，它提供了连接池的特性，“连接池”在通常情况下可以有效的提高应用的通信能力，并且这是一种良好的设计模式。Jedis的连接池设计基于apache commons-pool原生库，仅作了简单的封装；在本文中，我将介绍如何使用jedisPool进行程序设计。
 
#### 一.连接池基本参数详解

- maxActive: 链接池中最大连接数,默认为8.
- maxIdle: 链接池中最大空闲的连接数,默认为8.
- minIdle: 连接池中最少空闲的连接数,默认为0. 当空闲小于最小空闲是创建连接保证取到的连接是被建立的不用心建立连接保证性能
- maxWait: 当连接池资源耗尽时，调用者最大阻塞的时间，超时将跑出异常。单位，毫秒数;默认为-1.表示永不超时.
- minEvictableIdleTimeMillis: 连接空闲的最小时间，达到此值后空闲连接将可能会被移除。负值(-1)表示不移除。
- softMinEvictableIdleTimeMillis: 连接空闲的最小时间，达到此值后空闲链接将会被移除，且保留“minIdle”个空闲连接数。默认为-1.
- numTestsPerEvictionRun: 对于“空闲链接”检测线程而言，每次检测的链接资源的个数。默认为3.
- testOnBorrow: 向调用者输出“链接”资源时，是否检测是有有效，如果无效则从连接池中移除，并尝试获取继续获取。默认为false。建议保持默认值.
- testOnReturn:  向连接池“归还”链接时，是否检测“链接”对象的有效性。默认为false。建议保持默认值.
- testWhileIdle:  向调用者输出“链接”对象时，是否检测它的空闲超时；默认为false。如果“链接”空闲超时，将会被移除。建议保持默认值.
- timeBetweenEvictionRunsMillis:  “空闲链接”检测线程，检测的周期，毫秒数。如果为负值，表示不运行“检测线程”。默认为-1.
- whenExhaustedAction: 当“连接池”中active数量达到阀值时，即“链接”资源耗尽时，连接池需要采取的手段, 默认为1：
 -> 0 : 抛出异常，
 -> 1 : 阻塞，直到有可用链接资源
 -> 2 : 强制创建新的链接资源

#### 二.程序实例

``` java
public class PoolTestMain {  
    public static void main(String[] args) {  
        JedisPoolConfig config = new JedisPoolConfig();  
        config.setMaxActive(32);  
        config.setMaxIdle(6);  
        config.setMinIdle(0);  
        config.setMaxWait(15000);  
        config.setMinEvictableIdleTimeMillis(300000);  
        config.setSoftMinEvictableIdleTimeMillis(-1);  
        config.setNumTestsPerEvictionRun(3);  
        config.setTestOnBorrow(false);  
        config.setTestOnReturn(false);  
        config.setTestWhileIdle(false);  
        config.setTimeBetweenEvictionRunsMillis(60000);//一分钟  
        config.setWhenExhaustedAction((byte)1);  
        JedisPool pool = new JedisPool(config,"127.0.0.1",6379,15000,"0123456",12);  
        Jedis client = pool.getResource();//从pool中获取资源  
        try{  
            client.select(0);  
            client.set("k1", "v1");  
            System.out.println(client.get("k1"));  
        }catch(Exception e){  
            e.printStackTrace();  
        }finally{  
            pool.returnResource(client);//向连接池“归还”资源，千万不要忘记。  
        }  
    }  
}  
```

#### 三.Spring与Jedis连接池

#####     1) beans.xml: 位于/resources/beans.xml

``` xml
<beans xmlns="http://www.springframework.org/schema/beans"   
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd" default-autowire="byName">  
    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">  
        <property name="maxActive" value="32"></property>  
        <property name="maxIdle" value="6"></property>  
        <property name="maxWait" value="15000"></property>  
        <property name="minEvictableIdleTimeMillis" value="300000"></property>  
        <property name="numTestsPerEvictionRun" value="3"></property>  
        <property name="timeBetweenEvictionRunsMillis" value="60000"></property>  
        <property name="whenExhaustedAction" value="1"></property>  
    </bean>  
    <bean id="jedisPool" class="redis.clients.jedis.JedisPool" destroy-method="destroy">  
        <!-- config -->  
        <constructor-arg ref="jedisPoolConfig"></constructor-arg>  
        <!-- host -->  
        <constructor-arg value="127.0.0.1"></constructor-arg>  
        <!-- port -->  
        <constructor-arg value="6379"></constructor-arg>  
        <!-- timeout -->  
        <constructor-arg value="15000"></constructor-arg>  
        <!-- password -->  
        <constructor-arg value="0123456"></constructor-arg>  
        <!-- database index -->  
        <constructor-arg value="12"></constructor-arg>        
    </bean>  
</beans>  
```

 #####   2) 测试类：不过在实际的spring环境中，只需要“注入”即可.

``` java
public static void main(String[] args) {  
    //resources/beans.xml  
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("classpath:beans.xml");  
    JedisPool jedisPool = (JedisPool)context.getBean("jedisPool");  
    Jedis client = jedisPool.getResource();  
    try{  
        client.select(0);  
        client.set("k1", "v1");  
        System.out.println(client.get("k1"));  
    }catch(Exception e){  
        e.printStackTrace();  
    }finally{  
        jedisPool.returnResource(client);//must be  
    }  
}  
```

#### 四.Spring-data集成

    上述代码，事实上存在设计上尚有改进之处，spring-data中对redis的支持，在编程级别更加轻便，<此方面内容将会在稍后专门介绍.>
