---
layout: post
title: 多线程
date:   2018-06-06 20:36:11 +08:00
category: 多线程
tags: 多线程
comments: true
---

* content
{:toc}






### 概述

当一个程序运行在多线程下，就好像有多个 CPU 在同时执行该程序。多线程是在同一个程序内部并行执行，因此会对相同的内存空间进行并发读写操作。

多线程设计更复杂，访问共享数据的时候，这部分代码需要特别的注意。上下文切换存在开销，执行一个线程切换到执行另外一个线程的时候，它需要先存储当前线程的本地的数据，程序指针等。线程还需要一些内存来维持它本地的堆栈，也就增加资源消耗。
### Java的线程运行模型
#### **线程间的交互**

所有变量都存储在主内存中，每条线程还有自己的工作内存，并保存了使用到的内存到主内存的副本拷贝，这样读写操作都是在工作内存中，互不影响且速度更快，但是线程之间传递需要主内存。工作内存会通过save和load来保证与主内存数据一致。注意，这里的内存模型是JVM底层实现模型和JVM规范的内存模型并不是一个层面的东西。
#### 指令重排
CPU和编译器为了提升程序执行的效率，提高程序运行效率，在不影响单线程程序执行结果的前提下，尽可能地提高并行度，重排会导致运行顺序和预想不同。
例如：在A线程代码中看似无顺序的两行语句，影响B线程中的结果，根据指令重排就容易出现错误，因为重排A的代码不会考虑B的逻辑。
#### 线程概念
### 多线程模型特征
#### 原子性

一个操作或多个操作要么全部执行完成且执行过程不被中断，要么就不执行。真正原子操作是JVM的执行指令（read、load、write等），而不是Java语言中的某个语句（count++不是原子性的，构造函数也不是）。
构造函数不是原子性，包括了分配内存、初始化、设置引用指向内存，其中第二三项的顺序不保证，这就是双重锁的危险点。

#### 可见性

当多个线程同时访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。线程之间数据不同的原因就是工作内存与主内存的工作方式。
多线程中不加volatile的属性即使在代码逻辑看只有一个，在实际运行中不同线程也会有自己的副本，就会导致修改的值无法及时同步到其它线程。

#### 有序性

JVM对于在本线程中所有的操作都是有先后顺序的，但是当两个线程放在一起看，先后顺序是不保证的，原因是存在指令重排和内存同步延迟（共作内存和主内存）。
由于指令重排，会导致原子操作先后顺序发生变化，但是很多Java语句不是原子性，一旦重排会导致多个线程看实际JVM操作顺序是不保证的，比如对象创建时，另一个线程可能获取到一个未初始化完成的对象。

##### 先行发生原则

这其实是一个判断规则，假如A操作和B操作先后发生，而且B操作可以看到A操作的影响（改变值等），那么这就A和B是先行发生。但是由于原子性、可见性、有序性在多线程中不保证，所以很多操作（代码逻辑）看似是先后发生，但是实际不是
	
### 线程安全
允许多个线程同时执行的代码就是线程安全的代码，但是由于JVM的线程内存模型、指令重排和对象引用等等原因，导致线程安全的代码需要仔细考虑，甚至是看似安全的代码其实存在隐患。
在多线程中，假如没有访问共享数据，那么基本就是安全的，一旦存在数据竞争存在隐患。真正线程安全的代码是封装了正确性保障手段，领调用者不用关心多线程问题。
#### 局部变量
局部变量存储在线程自己的栈中。也就是说，局部变量永远也不会被多个线程共享。但是局部变量中的对象引用比较特别，尽管引用本身是没有被共享的，但是指向的对象存储在堆中，所以是被共享的，除非这个对象引用不会逃逸（对象在方法内创建，但是可以在方法外拿到引用）。
#### 对象成员
同一个对象的同一个成员被多个线程访问，那么此成员就不是线程安全的。当然，如果成员是对象，而且不包含数据，只提供方法服务，而且方法也是线程安全的，实际也是线程安全的。
#### 并发控制
代码中符合先行发生原则的操作基本是不用并发控制的，也就是线程安全的，只有逻辑上希望是先行发生，但实际运行不一定时，需要使用并发控制来保证三原则。
#### volatile关键字
使用volatile关键字强迫所有线程读写主内存，从而使得变量在多线程中是可见的，同时通过“内存屏障”（主要是构造函数执行时，引用可以使用之前保证完成初始化），避免出现指令重排，保证了可见性和有序性。但是不会保证原子性，比如count++语句依然是多个指令组合完成。	


#### synchronized关键字
可以修饰方法和代码块，包括静态方法。
synchronized使用的是monitorenter和monitorexit指令实现，monitorenter指令插入到同步代码块的开始位置，monitorexit指令插入到同步代码块的结束位置，JVM需要保证每一个monitorenter都有一个monitorexit与之相对应。任何对象都有一个monitor与之相关联，当且一个monitor被持有之后，他将处于锁定状态。线程执行到monitorenter指令时，将会尝试获取对象所对应的monitor所有权，即尝试获取对象的锁。
当synchronized修饰static方法时，相当于使用JVM中的该类的Class的对象作为锁，当修饰不是static方法时，相当于使用该对象作为锁，如果一个类多个方法使用synchronized修饰，那么static的用的都是一个锁，非static的用的也都是一个锁。
锁是针对的线程，而不是方法调用，也就是说线程中调用synchronized方法，方法里面又调用了一个synchronized方法，只要这两个方法用的是一个锁就可以全部通过。简单说，线程拿到一个锁之后，可以进入所有这个锁对应的方法。
此关键字保证了原子性和可见性，代码块中的操作会由一个线程一次性完成，而且修改了数据值后会立刻同步到别的线程。
##### AQS队列同步器
AbstactQueuedSynchronizer是并发同步组件的基础框架，它简化了锁等组件的实现，简化了包括同步状态管理、线程排队、等待与呼唤等底层操作。
队列同步器提供的是模板方法，需要子类去实现。包括三类方法：独占获取与释放同步状态、共享获取与释放同步状态、查询同步队列的等待线程。同步器包括一个同步队列和一个int类型同步状态。
同步状态：独占获取同步状态时使用的CAS操作。
同步队列：同步队列是一个双向队列，有两个引用一个指向头结点，一个指向尾结点。当线程无法获取锁的时候，需要加入队列，这时使用的CAS操作保证线程安全。
独占式获取和释放同步状态：使用CAS操作尝试获取同步状态（设置同步状态为1），如果失败就把线程加入到同步队列，这里使用自旋和CAS。然后线程进入自旋等待，并进入挂起状态。释放同步状态会唤起头节点的后继节点，唤起的节点会判断前节点是否是头结点，如果是则可以移出队列并获取同步状态，释放同步状态的肯定是一个线程所以不会使用CAS操作。这里挂起和唤醒使用的是LockSupport。
共享式同步状态的获取与释放：获取同步状态时如果返回值大于等于0就可以获取同步状态，释放的时候通过使用CAS来保证线程安全。
#### Lock类
Lock 的锁定是JDK代码实现的，Lock本身是一个接口，而 synchronized 是在 JVM 层面上实现的，而且Lock需要代码手动释放，不然会出现死锁。Lock包含很多方法包括tryLock（非阻塞立即返回锁状态，或者尝试一段时间），lockInterruptibly（Thread.interrupt可以中断线程的等待状态，中断是会在此处抛异常的，但是lock方法在等待时不会被中断）等。
有很多Lock的实现类，常用的有ReentrantLock、ReadWriteLock等，其实现都依赖java.util.concurrent.AbstractQueuedSynchronizer类，这个类有两个实现类一个用于公平锁一个用于非公平锁。AQS实现的是一个CLH锁的一种变形，CLH锁也是一种基于链表的可扩展、高性能、公平的自旋锁。
两个核心元素是表锁状态的变量和队列，状态变量使用voaltile和CAS操作保证原子性和可见性，而队列中的节点表示因未能获取锁而阻塞的线程。大致过程就是状态变量为0时获取锁，为1就把线程放队列里，一旦执行完成就1变回0并从队列里唤起线程。JDK中使用的是Unsafe类进行线程挂起和CAS操作。
锁可以分为公平锁和不公平锁，重入锁和非重入锁，以上过程实际上是非公平锁的获取和释放过程。公平锁严格按照先来后到的顺去获取锁，而非公平锁允许插队获取锁。重入锁是线程可以进入任何一个它已经拥有的锁所同步着的代码块。而且还有读写锁，就是把一个资源的读和写分开加锁。
##### AQS
AQS是JDK中实现众多并发控制的基础，在Lock的加锁和释放锁如下：

1. 线程尝试设置state状态为1，这里使用Unsafe类的CAS操作，假如原先为0则进入代码（临界区），这里公平锁的区别是只允许链表中排队首节点进行CAS操作，非公平锁是刚插入的节点会有机会进行CAS操作
2. 设置失败就开始尝试获取，如果当前线程就是锁的拥有者则获取成功，否则state+1并创建节点进入队列操作
3. 节点就是当前线程，这里并再次尝试获取，如果成功则节点是双向链表头并进入临界区，否则使用Unsafe类进行park操作，这里线程是自旋的（死循环），一旦从park状态唤醒就会再次进行第3步
4. 线程进入临界区操作之后，开始尝试设置state-1，成功之后就会将队列下一个节点从park唤起

#### Semaphore

一个计数信号量，原理和Lock类似，只是信号量是计数的，可以允许多个线程进入代码，底层实现和Lock一样使用的是AQS类。区别在于这里使用的是共享模式的AQS，并且state作为信号量大小，用来记录当前可以有几个线程获取临界区，共享就是唤起线程获得许可之后会继续唤起下一个节点。
#### ThreadLocal

使用ThreadLocal的get、set等操作，实际是通过Thread.currentThread拿到当前线程，然后从当前线程中取ThreadLocalMap，ThreadLocalMap的类定义在ThreadLocal中，但其实例实际在Thread对象中。这里ThreadLocalMap使用了Entry，但是key值不是Thread，而是ThreadLocal，从而避免冲突访问。从ThreadLocal取值是，以ThreadLocal当做key，从当前Thread中的ThreadLocalMap取出对应的value对象。
ThreadLocalMap使用的是开放定址法来解决散列冲突（hash值相同），也就是hash值相同且被占用，就数组向后查空值来用。




#### 原子类
原子类使用CAS（CompareAndSwap）的思路来保证数据冲突问题，也可以看做是乐观锁的一种实现。底层实现使用的也是Unsafe类，使用的是native方法，基于CPU的指令完成的，与平台相关。
详解：incrementAndGet方法逻辑是，先获取当前值，然后进行增加操作，写回volatile属性之前会进行比较，如果当前值和修改前的值相同才会写回，否则循环至第一步。这里会有ABA的问题，就是读取时是A，但实际是A变成B又变回A的原因
#### CountDownLatch
CountDownLatch用来计数，await的线程需要对应数量的线程触发countDown方法才会继续下去，使用ABS实现的是个共享锁模式，也就是可以多个await线程同时获取锁。
Countdown的数量就是同步队列的state值，await的线程会排队到同步队列中。每一次countDown都是通过CAS操作对state进行减操作，变为0时就会将队列链式唤醒，也就是每个节点都是通知状态，恢复执行后会去唤醒下一个节点。
### 线程通信
#### 等待/通知机制
Object类有wait、notify和notifyAll方法是本地方法，并且为final方法，无法被重写。调用某个对象的wait方法能让当前线程阻塞，并且当前线程必须拥有此对象的monitor（即锁）调用某个对象的notify()方法能够唤醒一个正在等待这个对象的monitor的线程，如果有多个线程都在等待这个对象的monitor，则只能唤醒其中一个线程；调用notifyAll()方法能够唤醒所有正在等待这个对象的monitor的线程。
如果调用某个对象的wait()方法，当前线程必须拥有这个对象的monitor（即锁），因此调用wait()方法必须在同步块或者同步方法中进行（synchronized块或者synchronized方法）。调用某个对象的wait()方法，相当于让当前线程交出此对象的monitor，然后进入等待状态，等待后续再次获得此对象的锁。调用某个对象的notify()方法，当前线程也必须拥有这个对象的monitor，因此调用notify()方法必须在同步块或者同步方法中进行。notify()和notifyAll()方法只是唤醒等待该对象的monitor的线程，并不决定哪个线程能够获取到monitor。
#### Condition
Condition定义了await和signal两个方法，而且需要与Lock对象关联，就是用newCondition来创建。await方法后，线程会释放Lock锁并在此等待，而其他线程调用 signal方法，通知当前线程从await返回，如果当前等待线程从await方法返回，那么表明当前线程已经获取了锁。不论是await和signal的前提都是线程拿到锁。
实现依赖的是AbstractQueuedSynchronizer的内部类ConditionObject，因为需要操作关联的Lock锁。每个Condition对象包含一个等待队列，await就会释放锁、构造节点、加入等待队列，因为线程已经获取锁了所以不需要CAS。

![](/assets/images4post/thread-1234452345.png)

await的过程是获取锁的线程从同步队列进入等待队列，这样锁被释放。

![](/assets/images4post/thread-123445234asd5.png)

signal方法是唤起等待时间最长的节点进入同步队列，这样当别的线程释放锁的时候，新加入的这个节点就有机会去竞争锁。

![](/assets/images4post/thread-12344523sd45.png)

#### Thread.join
主线程调用子线程对象的join方法会阻塞到子线程完成。join方法会调用Thread对象的wait方法，但是并没有执行notify方法，因为这个是在jvm中实现的, 一个线程执行结束后会执行该线程自身对象的notifyAll方法
### 并发框架
#### Fork/Join
#### Executor
#### FutureTask



















	
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

