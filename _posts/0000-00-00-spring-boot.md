---
title: Spring boot
layout: post
date: 2018-06-06 17:30:18 +0800
categories: spring
tags: boot
---
* content
{:toc}




``` java
@Configuration
@EnableAutoConfiguration
@ComponentScan
public class SpringBootMakerApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootMakerApplication.class, args);
    }
}
```
或
``` java
@SpringBootApplication
public class SpringBootMakerApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootMakerApplication.class, args);
    }
}
```

@Configuration：指出该类是 Bean 配置的信息源，相当于XML中的<beans></beans>，一般加在主类上。

@EnableAutoConfiguration：让 SpringBoot 根据应用所声明的依赖来对 Spring 框架进行自动配置，由于 spring-boot-starter-web 添加了Tomcat和Spring MVC，所以auto-configuration将假定你正在开发一个web应用并相应地对Spring进行设置

@ ComponentScan：表示将该类自动发现（扫描）并注册为Bean，可以自动收集所有的Spring组件（@Component , @Service , @Repository , @Controller 等），包括@Configuration类。

@SpringBootApplication： @EnableAutoConfiguration、@ComponentScan和@Configuration的合集。

@ EnableTransactionManagement：启用注解式事务。


### 参考
> [IDEA中配置及使用Docker](https://blog.csdn.net/jacksonary/article/details/78974344)<br/>
> [【案例】将web部署到wildfly中](https://blog.csdn.net/jacksonary/article/details/78974344)<br/>

> [在Intellij Idea中使用Docker运行spring-boot应用](https://blog.csdn.net/smnzg/article/details/79337454)





