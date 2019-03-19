---
layout: post
title: 多线程
date:   2018-06-06 20:36:11 +08:00
category: java
tags: 多线程
comments: true
---

* content
{:toc}








## 什么是多线程,为什么用多线程

介绍多线程之前要介绍线程，介绍线程则离不开**进程**。
* **进程**：是一个正在执行中的程序，每一个进程执行都有一个执行顺序，该顺序是一个执行路径，或者叫一个控制单元
* **线程**：就是进程中的一个独立控制单元，线程在控制着进程的执行。一个进程中至少有一个进程
* **多线程**：一个进程中不只有一个线程

### 为什么要用多线程：

　　　　①、为了更好的利用cpu的资源，如果只有一个线程，则第二个任务必须等到第一个任务结束后才能进行，如果使用多线程则在主线程执行任务的同时可以执行其他任务，而不需要等待；

　　　　②、进程之间不能共享数据，线程可以；

　　　　③、系统创建进程需要为该进程重新分配系统资源，创建线程代价比较小；

　　　　④、Java语言内置了多线程功能支持，简化了java多线程编程。


### 线程的状态

``` java
    public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
```

![](https://upload-images.jianshu.io/upload_images/1689841-af3e5b75b44e972c.png?imageMogr2/auto-orient/strip%7CimageView2/2)

![](https://upload-images.jianshu.io/upload_images/1689841-383f7101e6588094.png?imageMogr2/auto-orient/strip%7CimageView2/2)　

线程在Running的过程中可能会遇到阻塞(Blocked)情况

* 调用join()和sleep()方法，sleep()时间结束或被打断，join()中断,IO完成都会回到Runnable状态，等待JVM的调度。

* 调用wait()，使该线程处于等待池(wait blocked pool),直到notify()/notifyAll()，线程被唤醒被放到锁定池(lock blocked pool )，释放同步锁使线程回到可运行状态（Runnable）

* 对Running状态的线程加同步锁(Synchronized)使其进入(lock blocked pool ),同步锁被释放进入可运行状态(Runnable)。

此外，在runnable状态的线程是处于被调度的线程，此时的调度顺序是不一定的。Thread类中的yield方法可以让一个running状态的线程转入runnable。

![](/assets/images4post/thread-status-383f7101e6588094.png)

```java
// 开始线程  
public void start( );  
public void run( );  
 
// 挂起和唤醒线程  
public void resume( );     // 不建议使用  
public void suspend( );    // 不建议使用  
public static void sleep(long millis);  
public static void sleep(long millis, int nanos);  
public final native void wait() throws InterruptedException;
public final native void notify();
public final native void notifyAll();
 
// 终止线程  
public void stop( );       // 不建议使用  
public void interrupt( );  
 
// 得到线程状态  
public boolean isAlive( );  
public boolean isInterrupted( );  
public static boolean interrupted( );  
 
// join方法  
public void join( ) throws InterruptedException;   //保证线程的run方法完成后程序才继续运行
```


### 创建线程的方式

比较常见的一个问题了，一般就是两种：

（1）继承Thread类

（2）实现Runnable接口

线程在建立后并不马上执行run方法中的代码，而是处于等待状态。
线程处于等待状态时，可以通过Thread类的方法来设置线程各种属性，如线程的优先级（setPriority）、线程名(setName)和线程的类型（setDaemon）等

### start()方法和run()方法的区别

只有调用了start()方法，才会表现出多线程的特性，不同线程的run()方法里面的代码交替执行。
如果只是调用run()方法，那么代码还是同步执行的，必须等待一个线程的run()方法里面的代码全部执行完毕之后，另外一个线程才可以执行其run()方法里面的代码。

当调用start方法后，线程开始执行run方法中的代码。
线程进入运行状态。可以通过Thread类的isAlive方法来判断线程是否处于运行状态。
当线程处于运行状态时，isAlive返回true，当isAlive返回false时，可能线程处于等待状态，也可能处于停止状态。

----
## 参考链接


>* [多线程全面详解总结](https://www.cnblogs.com/yjboke/p/8911220.html)
>* [Java中的多线程你只要看这一篇就够了](https://www.cnblogs.com/wxd0108/p/5479442.html)
>* [Java多线程-线程的生命周期](https://www.cnblogs.com/linjiqin/p/3208494.html)
>* [40个Java多线程问题总结](http://baijiahao.baidu.com/s?id=1579957724724090222&wfr=spider&for=pc)

>* [架构师养成](https://www.cnblogs.com/sigm/tag/%E6%9E%B6%E6%9E%84%E5%B8%88%E5%85%BB%E6%88%90/default.html?page=2)





----

