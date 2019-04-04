---
title: proxy
layout: post
date: 2017-11-14 17:57:30 +0800
categories: 代理
tags: 代理
---


* content
{:toc}                                                                                                          











###

```

    public <T> T factoryProxy() {
        if (this.master == null) {
            throw new SwitchExecption("配置master is null error,class=" + SwitchProxy.class.getName());
        } else {
            SwitchInvocationHandler switchInvocationHandler = new SwitchInvocationHandler();
            switchInvocationHandler.setMaster(this.master);
            switchInvocationHandler.setSpare(this.spare);
            switchInvocationHandler.setFailTimeout(this.failTimeout);
            switchInvocationHandler.setRouteConfig(this.routeConfig);
            return Proxy.newProxyInstance(this.master.getClass().getClassLoader(), this.master.getClass().getInterfaces(), switchInvocationHandler);
        }
    }

```

```
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        String key = Keys.getKey(this.master, method);
        CallerInfo callerInfo = Profiler.registerInfo(UMPKey.buildKey(key), AppConfig.appName, false, true);
        if (DownGradeRateUtils.isDownGrade(this.master.getClass(), method.getName())) {
            return SwitchContext.getDownGradeResult(this.master.getClass(), method.getName());
        } else {
            SwitchContext switchContext = ClassLoaderContext.getApplicationContext();
            Map<String, SwitchOpera> switchModule = switchContext.getSwitchModule();
            SwitchOpera switchOpera = (SwitchOpera)switchModule.get(key);
            if (this.routeConfig != null) {
                try {
                    switchOpera = this.routeConfig.getSwitchOpera(Keys.getKey(this.master.getClass()));
                } catch (Throwable var29) {
                    ;
                }
            }

            Object result = null;

            try {
                if (this.spare != null && switchOpera == SwitchOpera.SPARE) {
                    result = method.invoke(this.spare, args);
                } else {
                    result = method.invoke(this.master, args);
                }
            } catch (Throwable var30) {
                Profiler.functionError(callerInfo);
                Map<String, Counter> switchModuleMap = switchContext.getCounter();
                Counter counter = (Counter)switchModuleMap.get(key);
                if (counter == null) {
                    counter = new Counter();
                    switchModuleMap.put(key, counter);
                }

                if (counter.getTime() < System.currentTimeMillis()) {
                    counter.setTime(System.currentTimeMillis() + (long)(this.failTimeout * 1000));
                    counter.MasterZero();
                    counter.SpareZero();
                }

                if (this.spare != null && switchOpera == SwitchOpera.SPARE) {
                    counter.incrSpareFails();
                } else {
                    counter.incrMasterFails();
                }

                if (this.spare == null) {
                    throw new RuntimeException(var30);
                }

                if (switchOpera == SwitchOpera.MASTER) {
                    throw new RuntimeException(var30);
                }

                CallerInfo spareCallerInfo = Profiler.registerInfo(UMPKey.buildKey(key + ".spare"), AppConfig.appName, false, true);

                try {
                    result = method.invoke(this.spare, args);
                } catch (Throwable var27) {
                    Profiler.functionError(spareCallerInfo);
                    counter.incrSpareFails();
                    throw new RuntimeException(var27);
                } finally {
                    Profiler.registerInfoEnd(spareCallerInfo);
                }
            } finally {
                Profiler.registerInfoEnd(callerInfo);
            }

            return result;
        }
    }

```