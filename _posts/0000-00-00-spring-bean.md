---
title: Spring Bean
layout: post
date: 2017-02-09 17:30:18 +0800
categories: spring
tags: bean
---
* content
{:toc}
本文 spring复习笔记


- [Spring源码解析-beanfactory](http://blog.csdn.net/xiaoxufox/article/details/54601391)
- [spring的bean加载2](http://blog.csdn.net/long636/article/details/49364019)
- [spring源码深度解析(笔记四)--bean的加载](http://blog.csdn.net/ganxiaojieke/article/details/53749081)







### bean

#### init-method  

 * 该方法是由spring容器执行  
 * 在构造函数之后执行  
 * 如果在构造函数之后，在调用方法之前要做一些工作，可以在init方法中完成

#### destroy-method  

 * 如果该bean是单例(默认就是singlton)，则在spring容器关闭或者销毁的时候，执行该方法  
 * 如果该bean是多例(scope="prototype")，则spring容器不负责销毁  
 * 说明：要想让spring容器控制bean的生命周期，那么该bean必须是单例 如果该bean是多例，该bean中还有资源，关闭资源的操作由程序员完成

### scope
 
#### singleton  默认  

 * 单例
 * 属性是共享的
 * 一般情况下，把数据存放在方法中的变量中

对于Spring中实现Singleton模式，是以IOC容器为单位，换句话说，一个JVM可能有多个IOC容器，而Java中实现Singleton而言，只有一个JVM。所以说，Spring中实现的Singleton模式与Java中的Singleton设计模式有点不同。 
此外，spring singleton的含义是在一个spring 上下文中保持单态。 
所以singleton模式中，singleton的class在整个JVM中只有一个instance，Spring的Bean，你可以一个class配置多个Bean，这个class就有了多个instance。这个singleton是指在spring容器中，这个Bean是单实例的，是线程共享的。所以要求这些类都是线程安全的。也就是说，不能出现修改Bean属性的方法，当然除了设值得那些setter。只要满足线程安全，这些bean都可以用singleton。而且我们在绝大多数使用上，也是这样用的，包括dao,service。 

Beanfactory是Spring初始以静态方式载入的,Spring的单例IOC是基于容器级的,所以这你都不用担心与考虑. 

--应用中对象有两种，行为对象和数据对象，行为对象都要求是线程安全的！也就是允许单例的, 不管是dao 还是 service 对象，都是行为对象，行为对象不应该引用非线程安全的对象做成员量，同时在应用外部的资源（如文件，数据库连接，session）时，要先保证对这些东西的访问是做了并发控制的！ 

对于spring来讲，<bean scope="singleton"/>或<bean singleton="true"/>都是保证对同一sesionfactory bean是单例的，也就是所谓 sessionfactory 范围的. 

--这是一个真实的案例，我们在项目中使用Spring和ACEGI,我之所以选择ACEGI，除了它对权限的良好控制外， 

我还看好它的SecurityContextHolder，通过代码来看： 

``` java
Authentication auth = SecurityContextHolder.getContext().getAuthentication();
```

我可以很容易在系统任意一层得到用户的信息，而不用把用户信息在参数里传来传去，(这也是struts的缺点之一) 

但是我在每一次要得到用户信息的时候都写上面的一段代码，未免有些麻烦，所以我在BaseService, BaseDao里都提供了如下方法： 

``` java
/**   
 * get current login user info   
 * @return UserInfo   
 */    
protected UserInfo getUserInfo()     
{     
    return getUserContext().getUserInfo();     
}     
    
/**   
 * get current login user context   
 * @return UserContext   
 */    
protected UserContext getUserContext()     
{     
    Authentication auth = SecurityContextHolder.getContext().getAuthentication();     
    return (UserContext) auth.getPrincipal();     
}     
```


这样在其他的Service和Dao类里可以通过 

``` java
super.getUserContext(), super.getUserInfo()     
```

来得到用户的信息，这也为问题的产生提供了温床。请看如下代码： 

``` java
public class SomeServece extends BaseService implements SomeInterFace       
{     
    private UserInfo user = super.getUserInfo();     
         
    public someMethod()     
    {     
       int userID = this.user.getUserID();     
       String userName = this.user.getUserName();     
       //bla bla do something user userID and userNaem     
    }     
}         
```

这段代码在单元测试的时候不会用任何问题，但是在多用户测试的情况下，你会发现任何调用SomeService里someMethod()方法的userID和userName都是同一个人，也就是第一个登陆的人的信息。 
其根本原因是Spring的Bean在默认情况下是Singleton的，Bean SomeServece的实例只会生成一份，也就是所SomeServece实例的user对象只会被初始化一次，就是第一次登陆人的信息，以后不会变了。所以BaseService想为开发提供方便，却给开发带来了风险. 

正确的用法应该是这样的 
``` java
public class SomeServece extends BaseService implements SomeInterFace       
{        
    public someMethod()     
    {     
       int userID = super.getUserInfo().getUserID();     
       String userName = super.getUserInfo().getUserName();     
       //bla bla do something user userID and userNaem     
    }  
}  
```

 
#### prototype

 * 多例
 * 当一个bean是多例模式的情况下，lazy-init为false或者default无效

### lazy-init

ApplicationContext实现的默认行为就是在启动时将所有singleton bean提前进行实例化。提前实例化意味着作为初始化过程的一部分，ApplicationContext实例会创建并配置所有的singleton bean。通常情况下这是件好事，因为这样在配置中的任何错误就会即刻被发现（否则的话可能要花几个小时甚至几天）。
通过在<beans/>元素上使用'default-lazy-init'属性、<bean />元素上使用'lazy-init'

#### lazy-init true  在context.getBean的时候才要创建对象  

*  优点 如果该bean中有大数据存在，则什么时候context.getBean,什么时候创建对象 可以防止数据过早的停留在内存中，做到了懒加载
*  缺点 如果spring配置文件中，该bean的配置有错误，那么在tomcat容器启动的时候，发现不了

#### lazy-init false 在启动spring容器的时候创建对象  

*  优点 如果在启动tomcat时要启动spring容器， 那么如果spring容器会错误，这个时候tomcat容器不会正常启动
*  缺点 如果存在大量的数据，会过早的停留在内存中

``` xml
<beans />                <bean />            immediately  
<beans />                <bean lazy-init="true" />   lazy      
<beans />                <bean lazy-init="false"/>   immediately           
<beans default-lazy-init="true"/>  <bean />          lazy  
<beans default-lazy-init="true"/>  <bean lazy-init="true" />     lazy  
<beans default-lazy-init="true"/>  <bean lazy-init="false" />    immediately  
<beans default-lazy-init="false"/>  <bean />             immediately  
<beans default-lazy-init="false"/>  <bean lazy-init="true" />    lazy  
<beans default-lazy-init="false"/>  <bean lazy-init="false" />   immediately  
```
