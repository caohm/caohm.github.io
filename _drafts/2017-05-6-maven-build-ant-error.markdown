---
title: 2017-05-6-maven-build-ant-error
layout: post
date:   +0800 
categories: []
tags: []
---


* content
{:toc}


本文介绍











### 

maven项目不能编译,老是报下面这个错误
maven
[INFO] Scanning for projects...
[INFO]

[INFO] ------------------------------------------------------------------------
[INFO] Building spring-mvc-study Maven Webapp 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ spring-mvc-study ---
[WARNING] Error injecting: org.apache.maven.plugin.resources.ResourcesMojo
java.lang.NoClassDefFoundError: org/apache/maven/shared/filtering/MavenFilteringException
at java.lang.Class.getDeclaredConstructors0(Native Method)
at java.lang.Class.privateGetDeclaredConstructors(Class.java:2493)
at java.lang.Class.getDeclaredConstructors(Class.java:1901)
at com.google.inject.spi.InjectionPoint.forConstructorOf(InjectionPoint.java:245)
at com.google.inject.internal.ConstructorBindingImpl.create(ConstructorBindingImpl.java:99)
at com.google.inject.internal.InjectorImpl.createUninitializedBinding(InjectorImpl.java:657)
at com.google.inject.internal.InjectorImpl.createJustInTimeBinding(InjectorImpl.java:875)
at com.google.inject.internal.InjectorImpl.createJustInTimeBindingRecursive(InjectorImpl.java:798)
at com.google.inject.internal.InjectorImpl.getJustInTimeBinding(InjectorImpl.java:281)
at com.google.inject.internal.InjectorImpl.getBindingOrThrow(InjectorImpl.java:213)
at com.google.inject.internal.InjectorImpl.getProviderOrThrow(InjectorImpl.java:998)
at com.google.inject.internal.InjectorImpl.getProvider(InjectorImpl.java:1031)
at com.google.inject.internal.InjectorImpl.getProvider(InjectorImpl.java:994)
at com.google.inject.internal.InjectorImpl.getInstance(InjectorImpl.java:1044)
at org.eclipse.sisu.space.AbstractDeferredClass.get(AbstractDeferredClass.java:48)
at com.google.inject.internal.ProviderInternalFactory.provision(ProviderInternalFactory.java:86)
at com.google.inject.internal.InternalFactoryToInitializableAdapter.provision(InternalFactoryToInitializableAdapter.java:54)
at com.google.inject.internal.ProviderInternalFactory$1.call(ProviderInternalFactory.java:70)
at com.google.inject.internal.ProvisionListenerStackCallback$Provision.provision(ProvisionListenerStackCallback.java:115)
at org.eclipse.sisu.bean.BeanScheduler$Activator.onProvision(BeanScheduler.java:176)
at com.google.inject.internal.ProvisionListenerStackCallback$Provision.provision(ProvisionListenerStackCallback.java:126)
at com.google.inject.internal.ProvisionListenerStackCallback.provision(ProvisionListenerStackCallback.java:68)
at com.google.inject.internal.ProviderInternalFactory.circularGet(ProviderInternalFactory.java:68)
at com.google.inject.internal.InternalFactoryToInitializableAdapter.get(InternalFactoryToInitializableAdapter.java:46)
at com.google.inject.internal.InjectorImpl$2$1.call(InjectorImpl.java:1009)
at com.google.inject.internal.InjectorImpl.callInContext(InjectorImpl.java:1059)
at com.google.inject.internal.InjectorImpl$2.get(InjectorImpl.java:1005)
at com.google.inject.internal.SingletonScope$1.get(SingletonScope.java:36)
at org.eclipse.sisu.inject.LazyBeanEntry.getValue(LazyBeanEntry.java:81)
at org.eclipse.sisu.plexus.LazyPlexusBean.getValue(LazyPlexusBean.java:51)
at org.codehaus.plexus.DefaultPlexusContainer.lookup(DefaultPlexusContainer.java:263)
at org.codehaus.plexus.DefaultPlexusContainer.lookup(DefaultPlexusContainer.java:255)
at org.apache.maven.plugin.internal.DefaultMavenPluginManager.getConfiguredMojo(DefaultMavenPluginManager.java:543)
at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo(DefaultBuildPluginManager.java:121)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute(MojoExecutor.java:208)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute(MojoExecutor.java:153)
at org.apache.maven.lifecycle.internal.MojoExecutor.execute(MojoExecutor.java:145)
at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject(LifecycleModuleBuilder.java:116)
at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject(LifecycleModuleBuilder.java:80)
at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build(SingleThreadedBuilder.java:51)
at org.apache.maven.lifecycle.internal.LifecycleStarter.execute(LifecycleStarter.java:128)
at org.apache.maven.DefaultMaven.doExecute(DefaultMaven.java:307)
at org.apache.maven.DefaultMaven.doExecute(DefaultMaven.java:193)
at org.apache.maven.DefaultMaven.execute(DefaultMaven.java:106)
at org.apache.maven.cli.MavenCli.execute(MavenCli.java:862)
at org.apache.maven.cli.MavenCli.doMain(MavenCli.java:286)
at org.apache.maven.cli.MavenCli.main(MavenCli.java:197)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
at java.lang.reflect.Method.invoke(Method.java:606)
at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced(Launcher.java:289)
at org.codehaus.plexus.classworlds.launcher.Launcher.launch(Launcher.java:229)
at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode(Launcher.java:415)
at org.codehaus.plexus.classworlds.launcher.Launcher.main(Launcher.java:356)
at org.codehaus.classworlds.Launcher.main(Launcher.java:47)
Caused by: java.lang.ClassNotFoundException: org.apache.maven.shared.filtering.MavenFilteringException
at org.codehaus.plexus.classworlds.strategy.SelfFirstStrategy.loadClass(SelfFirstStrategy.java:50)
at org.codehaus.plexus.classworlds.realm.ClassRealm.unsynchronizedLoadClass(ClassRealm.java:271)
at org.codehaus.plexus.classworlds.realm.ClassRealm.loadClass(ClassRealm.java:247)
at org.codehaus.plexus.classworlds.realm.ClassRealm.loadClass(ClassRealm.java:239)
... 56 more
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.955 s
[INFO] Finished at: 2015-08-24T11:16:53+08:00
[INFO] Final Memory: 8M/102M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-resources-plugin:2.6:resources (default-resources) on project spring-mvc-study: Execution default-resources of goal org.apache.maven.plugins:maven-resources-plugin:2.6:resources failed: A required class was missing while executing org.apache.maven.plugins:maven-resources-plugin:2.6:resources: org/apache/maven/shared/filtering/MavenFilteringException
[ERROR] -----------------------------------------------------
[ERROR] realm = plugin>org.apache.maven.plugins:maven-resources-plugin:2.6
[ERROR] strategy = org.codehaus.plexus.classworlds.strategy.SelfFirstStrategy
[ERROR] urls[0] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/org/apache/maven/plugins/maven-resources-plugin/2.6/maven-resources-plugin-2.6.jar
[ERROR] urls[1] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/org/apache/maven/reporting/maven-reporting-api/2.0.6/maven-reporting-api-2.0.6.jar
[ERROR] urls[2] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/org/apache/maven/doxia/doxia-sink-api/1.0-alpha-7/doxia-sink-api-1.0-alpha-7.jar
[ERROR] urls[3] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/commons-cli/commons-cli/1.0/commons-cli-1.0.jar
[ERROR] urls[4] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/org/codehaus/plexus/plexus-interactivity-api/1.0-alpha-4/plexus-interactivity-api-1.0-alpha-4.jar
[ERROR] urls[5] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/junit/junit/3.8.1/junit-3.8.1.jar
[ERROR] urls[6] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/org/codehaus/plexus/plexus-utils/2.0.5/plexus-utils-2.0.5.jar
[ERROR] urls[7] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/org/apache/maven/shared/maven-filtering/1.1/maven-filtering-1.1.jar
[ERROR] urls[8] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/org/sonatype/plexus/plexus-build-api/0.0.4/plexus-build-api-0.0.4.jar
[ERROR] urls[9] = file:/D:/program%20Files/maven/apache-maven-3.3.1/maven-local/org/codehaus/plexus/plexus-interpolation/1.13/plexus-interpolation-1.13.jar
[ERROR] Number of foreign imports: 1
[ERROR] import: Entry[import from realm ClassRealm[maven.api, parent: null]]
[ERROR] 
[ERROR] -----------------------------------------------------: org.apache.maven.shared.filtering.MavenFilteringException
[ERROR] -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/PluginContainerException



#### 解决办法

更改maven3.3.9到3.2.5没好使
更改maven3.2.5到3.0.5没好使
删除maven仓库的ant所有jar重新编译解决
