---
title: spring-exception
layout: post
date: 2017-12-01 17:57:16 +0800
categories: [spring]
tags: exception
---


* content
{:toc}

- [从线上问题谈spring生命周期类lifeCycle类和bean的生命周期](http://blog.csdn.net/fei33423/article/details/52331435)








### exception

#### BeanCurrentlyInCreationException

- log

```
Caused by: org.springframework.beans.factory.BeanCurrentlyInCreationException: Error creating bean with name 'elementServiceFactory': Requested bean is currently in creation: Is there an unresolvable circular reference?
    at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.beforeSingletonCreation(DefaultSingletonBeanRegistry.java:297)
    at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:216)
    at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:261)
    at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:185)
    at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:164)
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.findAutowireCandidates(DefaultListableBeanFactory.java:671)
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:610)
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireByType(AbstractAutowireCapableBeanFactory.java:1076)
    ... 97 more
```

##### 原因

相同的代码，不同机器的map的hash是不一样的，因此导致keyset之后，拿到的list排序也不一样，所以他的能起来。
因为他的类引用了有循环依赖问题的类，并将之正确常见出来，而我则在床两两个循环依赖的类的时候互相创建，导致创建失败。（注：不知道真实远离，我是这么理解的，知道原理的情指正，谢谢。）


#### BeanCreationNotAllowedException

- log

```
2017-11-30 23:01:19:886 ERROR [1330810362-5] org.springframework.beans.factory.BeanCreationNotAllowedException: Error creating bean with name 'umpAroundAdvice': Singleton bean creation not allowed while the singletons of this factory are in destruction (Do not request a bean from a BeanFactory in a destroy method implementation!)
    at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:212)
    at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:291)
    at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:193)
    at org.springframework.aop.config.SimpleBeanFactoryAwareAspectInstanceFactory.getAspectInstance(SimpleBeanFactoryAwareAspectInstanceFactory.java:63)
    at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethodWithGivenArgs(AbstractAspectJAdvice.java:621)
    at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethod(AbstractAspectJAdvice.java:610)
    at org.springframework.aop.aspectj.AspectJAroundAdvice.invoke(AspectJAroundAdvice.java:65)
    at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:172)
    at org.springframework.aop.interceptor.ExposeInvocationInterceptor.invoke(ExposeInvocationInterceptor.java:90)
    at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:172)
    at org.springframework.aop.framework.Cglib2AopProxy$DynamicAdvisedInterceptor.intercept(Cglib2AopProxy.java:622)
    at com.chmcc.sdk.business.address.AddressServiceImpl$$EnhancerByCGLIB$$27dd0aa1.getWholeAreaName(<generated>)
    at com.chmcc.sdk.service.jos.JOSAddressExportServiceImpl.getUsualAddressList(JOSAddressExportServiceImpl.java:371)
    at com.chmcc.sdk.service.jos.JOSAddressExportServiceImpl$$FastClassByCGLIB$$ce76285d.invoke(<generated>)
    at net.sf.cglib.proxy.MethodProxy.invoke(MethodProxy.java:191)
    at org.springframework.aop.framework.Cglib2AopProxy$CglibMethodInvocation.invokeJoinpoint(Cglib2AopProxy.java:689)
    at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:150)
    at org.springframework.aop.aspectj.MethodInvocationProceedingJoinPoint.proceed(MethodInvocationProceedingJoinPoint.java:80)
    at com.chmcc.sdk.common.utils.UMPMonitorAdvice.executeMethod(UMPMonitorAdvice.java:83)
    at com.chmcc.sdk.common.utils.UMPMonitorAdvice.doExportAround(UMPMonitorAdvice.java:47)
    at sun.reflect.GeneratedMethodAccessor119.invoke(Unknown Source)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
    at java.lang.reflect.Method.invoke(Method.java:597)
    at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethodWithGivenArgs(AbstractAspectJAdvice.java:621)
    at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethod(AbstractAspectJAdvice.java:610)
    at org.springframework.aop.aspectj.AspectJAroundAdvice.invoke(AspectJAroundAdvice.java:65)
    at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:172)
    at org.springframework.aop.interceptor.ExposeInvocationInterceptor.invoke(ExposeInvocationInterceptor.java:90)
    at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:172)
    at org.springframework.aop.framework.Cglib2AopProxy$DynamicAdvisedInterceptor.intercept(Cglib2AopProxy.java:622)
    at com.chmcc.sdk.service.jos.JOSAddressExportServiceImpl$$EnhancerByCGLIB$$4f7eb664.getUsualAddressList(<generated>)
    at sun.reflect.GeneratedMethodAccessor120.invoke(Unknown Source)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
    at java.lang.reflect.Method.invoke(Method.java:597)
    at com.chmcc.xsf.gd.filter.ProviderInvokeFilter.reflectInvoke(ProviderInvokeFilter.java:140)
    at com.chmcc.xsf.gd.filter.ProviderInvokeFilter.invoke(ProviderInvokeFilter.java:100)
    at com.chmcc.sdk.common.filter.ProviderFilter.invoke(ProviderFilter.java:28)
    at com.chmcc.xsf.gd.filter.ProviderConcurrentsFilter.invoke(ProviderConcurrentsFilter.java:62)
    at com.chmcc.xsf.gd.filter.ProviderTimeoutFilter.invoke(ProviderTimeoutFilter.java:39)
    at com.chmcc.xsf.gd.filter.ProviderMethodCheckFilter.invoke(ProviderMethodCheckFilter.java:78)
    at com.chmcc.xsf.gd.filter.ProviderInvokeLimitFilter.invoke(ProviderInvokeLimitFilter.java:54)
    at com.chmcc.xsf.gd.filter.ProviderHttpGWFilter.invoke(ProviderHttpGWFilter.java:47)
    at com.chmcc.xsf.gd.filter.ProviderGenericFilter.invoke(ProviderGenericFilter.java:118)
    at com.chmcc.xsf.gd.filter.ProviderContextFilter.invoke(ProviderContextFilter.java:73)
    at com.chmcc.xsf.gd.filter.ExceptionFilter.invoke(ExceptionFilter.java:49)
    at com.chmcc.xsf.gd.filter.SystemTimeCheckFilter.invoke(SystemTimeCheckFilter.java:79)
    at com.chmcc.xsf.gd.filter.FilterChain.invoke(FilterChain.java:275)
    at com.chmcc.xsf.gd.server.ProviderProxyInvoker.invoke(ProviderProxyInvoker.java:67)
    at com.chmcc.xsf.gd.server.JSFTask.doRun(JSFTask.java:123)
    at com.chmcc.xsf.gd.server.BaseTask.run(BaseTask.java:27)
    at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:441)
    at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:303)
    at java.util.concurrent.FutureTask.run(FutureTask.java:138)
    at java.util.concurrent.ThreadPoolExecutor$Worker.runTask(ThreadPoolExecutor.java:886)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:908)
    at java.lang.Thread.run(Thread.java:662)
```
- spring source

``` java

    /** Names of beans that are currently in creation */
    private final Set<String> singletonsCurrentlyInCreation = Collections.synchronizedSet(new HashSet<String>());

    /** Flag that indicates whether we're currently within destroySingletons */
    private boolean singletonsCurrentlyInDestruction = false;

    /**
     * Return the (raw) singleton object registered under the given name,
     * creating and registering a new one if none registered yet.
     * @param beanName the name of the bean
     * @param singletonFactory the ObjectFactory to lazily create the singleton
     * with, if necessary
     * @return the registered singleton object
     */
    public Object getSingleton(String beanName, ObjectFactory singletonFactory) {
        Assert.notNull(beanName, "'beanName' must not be null");
        synchronized (this.singletonObjects) {
            Object singletonObject = this.singletonObjects.get(beanName);
            if (singletonObject == null) {
                if (this.singletonsCurrentlyInDestruction) {
                    throw new BeanCreationNotAllowedException(beanName,
                            "Singleton bean creation not allowed while the singletons of this factory are in destruction " +
                            "(Do not request a bean from a BeanFactory in a destroy method implementation!)");
                }
                if (logger.isDebugEnabled()) {
                    logger.debug("Creating shared instance of singleton bean '" + beanName + "'");
                }
                beforeSingletonCreation(beanName);
                boolean recordSuppressedExceptions = (this.suppressedExceptions == null);
                if (recordSuppressedExceptions) {
                    this.suppressedExceptions = new LinkedHashSet<Exception>();
                }
                try {
                    singletonObject = singletonFactory.getObject();
                }
                catch (BeanCreationException ex) {
                    if (recordSuppressedExceptions) {
                        for (Exception suppressedException : this.suppressedExceptions) {
                            ex.addRelatedCause(suppressedException);
                        }
                    }
                    throw ex;
                }
                finally {
                    if (recordSuppressedExceptions) {
                        this.suppressedExceptions = null;
                    }
                    afterSingletonCreation(beanName);
                }
                addSingleton(beanName, singletonObject);
            }
            return (singletonObject != NULL_OBJECT ? singletonObject : null);
        }
    }

    public void destroySingletons() {
        if (logger.isInfoEnabled()) {
            logger.info("Destroying singletons in " + this);
        }
        synchronized (this.singletonObjects) {
            this.singletonsCurrentlyInDestruction = true;
        }

        synchronized (this.disposableBeans) {
            String[] disposableBeanNames = StringUtils.toStringArray(this.disposableBeans.keySet());
            for (int i = disposableBeanNames.length - 1; i >= 0; i--) {
                destroySingleton(disposableBeanNames[i]);
            }
        }

        this.containedBeanMap.clear();
        this.dependentBeanMap.clear();
        this.dependenciesForBeanMap.clear();

        synchronized (this.singletonObjects) {
            this.singletonObjects.clear();
            this.singletonFactories.clear();
            this.earlySingletonObjects.clear();
            this.registeredSingletons.clear();
            this.singletonsCurrentlyInDestruction = false;
        }
    }
```

##### 原因

tomcat未停服,请求不断的进来, 此时. spring正在关闭容器. context.close,destroy. 整个容器正在关闭中.
事务拦截器获取bean通过applicationContext里获取.判断容器正在关闭,拒绝获取bean.抛错.



### refrence

- [从线上问题谈spring生命周期类lifeCycle类和bean的生命周期](http://blog.csdn.net/fei33423/article/details/52331435)
- [spring 循环依赖引出的问题](http://phpxiaoxin.iteye.com/blog/991013)
