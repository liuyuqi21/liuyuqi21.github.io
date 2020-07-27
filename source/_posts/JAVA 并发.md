---
title: Java 并发
date: 2020-03-20 17:38:11
tags: Java
catrgories: Java
---
进程是应用程序在内存中分配的空间，也就是正在运行的程序。

CPU采用时间片轮转的方式运行进程：CPU为每个进程分配一个时间段，称作它的时间片。如果在时间片结束时进程还在运行，则暂停这个进程的运行，并且CPU分配给另一个进程（这个过程叫做上下文切换）。如果进程在时间片结束前阻塞或结束，则CPU立即进行切换，不用等待时间片用完。



# 并发编程的挑战

## 上下文切换

CPU通过时间片分配算法来循环执行任务，当前任务执行一个时间片后会切换到下一个任务。但是，在切换前会保存上一个任务的状态，以便下次切换回这个任务时，可以再加载这个任务的状态。所以任务从保存到再加载的过程就是一次上下文切换。

多线程不一定更快，因为线程有**创建和上下文切换**的开销。

减少上下文切换的方法有无锁并发编程、CAS 算法、使用最少线程和使用协程。

## 死锁

可以通过 dump 线程查看到底是哪个线程出现了问题。

避免死锁的方法：

- 避免一个线程同时获取多个锁
- 避免一个线程在锁内同时占用多个资源，尽量保证每个锁只占用一个资源
- 尝试使用定时锁，使用 `lock.tryLock(timeout)` 来替代使用内部锁机制
- 对于数据库锁，加锁和解锁必须在一个数据库连接里，否则会出现解锁失败的情况

# 并发机制的底层实现

## Voliate

Java语言提供了volatile，在某些情况下比锁要更加方便。如果一个字段被声明成volatile，Java线程内存模型确保所有线程看到这个变量的值是一致的。

作用于变量，只能保证有序性和可见性，不能保证原子性。

是轻量级的，他不会引起上下文切换和调度。

### 底层原理

在了解volatile实现原理之前，我们先来看下与其实现原理相关的CPU术语与说明。表2-1是CPU术语的定义。

| 术语       | 英文单词               | 术语描述                                                     |
| ---------- | ---------------------- | ------------------------------------------------------------ |
| 内存屏障   | memory barries         | 是一组处理器指令，用于实现对内存操作的顺序限制               |
| 缓冲行     | cache line             | 缓存可以分配的最小存储单位。处理器填写缓存线时会加载整个缓存线，需要使用多个主内存读周期 |
| 原子操作   | atomic poerations      | 不可中断的一个或一系列操作                                   |
| 缓存行填充 | cache line fill        | 当处理器识别到从内存中读取操作数是可缓存的，处理器读取整个缓存行到适当的缓存（L1，L2，L3 的或所有） |
| 缓存命中   | cache hit              | 如果进行高速缓存行填充操作的内存位置仍然是下次处理器访问的地址时，处理器从缓存中读取操作数，而不是从内存中读取 |
| 写命中     | write hit              | 当处理器将操作数写回到一个内存缓存的区域时，它首先会检查这个缓存的内存地址是否在缓存行中，如果存在一个有效的缓存行，则处理器将这个操作数写回到缓存，而不是写回到内存，这个操作被称为写命中 |
| 写缺失     | write misses the cache | 一个有效的缓存行被写入到不存在的内存区域                     |

通过 `volatile` 修饰的共享变量进行写操作的时候，转化为汇编代码时会有一个 `lock`前缀。

`lock` 前缀的指令在多核处理器下会引发两件事情：

1. 将当前处理器缓存行的数据写回到系统内存

1. 这个写回内存的操作会使在其他CPU里缓存了该内存地址的数据无效。

为了提高处理速度，处理器不直接和内存进行通信，而是先将系统内存的数据读到内部缓存（L1，L2或其他）后再进行操作，但操作完不知道何时会写到内存。如果对声明了volatile的变量进行写操作，JVM就会向处理器发送一条Lock前缀的指令，将这个变量所在缓存行的数据写回到系统内存。但是，就算写回到内存，如果其他处理器缓存的值还是旧的，再执行计算操作就会有问题。所以，在多处理器下，为了保证各个处理器的缓存是一致的，就会实现缓存一致性协议，每个处理器通过嗅探在总线上传播的数据来检查自己缓存的值是不是过期了，当处理器发现自己缓存行对应的内存地址被修改，就会将当前处理器的缓存行设置成无效状态，当处理器对这个数据进行修改操作的时候，会重新从系统内存中把数据读到处理器缓存里。

触发底层缓存锁定机制（缓存一致性协议&总线锁）。

例如触发 MESI 协议，lock 指令会触发**锁定变量缓存行**区域并写回主内存，这个操作称为“缓存锁定”。

![](https://gitee.com/cellophane/image/raw/master/20200409114425.png)

## synchronized 

synchorized 是 JVM 层面的实现，synchronized 关键字解决的是多个线程之间访问资源的同步性，synchronized 关键字 可以保证被它修饰的方法或者代码块在任意时刻只能有一个线程执行。

在 Java 早期版本中，synchronized 属于重量级锁，效率低下，因为监视器锁（monitor）是依赖于底层的操作系统的 Mutex Lock 来实现的，Java 的线程是映射到操作系统的原生线程之上的。如果要挂起或者唤醒一个线程，都需要操作系统帮忙 完成，而操作系统实现线程之间的切换时需要从用户态转换到内核态，这个状态之间 的转换需要相对比较长的时间，时间成本相对较高。

`synchorized` 同步块对同一条线程来说是可重入的，不会出现自己把自己锁死的问题。

### 使用方式

- 修饰实例方法：作用于当前对象实例加锁，进入同步代码前要获得当前对象实例的锁。

  ```java
  private static Object object = new Object();
  
  synchronized (Object){
      //代码
  }
  ```

- 修饰类方法：给当前类加锁，会作用于类的所有对象实例。如果一个线程 A 调用一个实例对象的非静态 synchronized 方法，而线程 B 需要调用这个实例对象所属类 的静态 synchronized 方法，是允许的，不会发生互斥现象，因为访问静态 synchronized 方法占用的锁 是当前类的锁，而访问非静态 synchronized 方法占用的锁是当前实例对象锁。

  ```java
  public static synchronized String func(){}
  ```

- 修饰代码块：指定加锁对象，对给定对象加锁，进入同步代码库前要获得给定对象的锁。

### 底层原理

#### 修饰同步语句块

`synchronized `关键字经过编译之后，会在同步块的前后分别形成 `monitorenter` 和 `monitorexit` 这两个字节码指令，这两个字节码都需要一个 `reference` 类型的参数来指明要锁定和解锁的对象。如果 Java 程序中的 `synchronized` 明确指定了对象参数，那就是这个对象的 `reference`；如果没有明确指定，那就根据 `synchronized` 修饰的是实例方法还是类方法，去取对应的对象实例或 `Class` 对象来作为锁对象。

#### 修饰方法

synchronized 修饰方法用 ACC_SYNCHRONIZED 标识来实现，该标识指明了该方法是一个同步方法，JVM 通过该 ACC_SYNCHRONIZED 访问标志来辨别一个方法是否声明为同步方法，从而执行相应的同步调用

### JDK 1.6 之后底层做的优化

Java 1.6 之后 Java 官方对从 JVM 层面对 synchronized 较大优化， JDK1.6 对锁的实现引入了大量的优化，如自旋锁、适应性自旋锁、锁消除、锁粗化、 偏向锁、轻量级锁等技术来减少锁操作的开销。

偏向锁就是在 MarkWord 上有一个当前持有线程的 id 和锁标识位，先检查锁标识位是不是偏向锁，然后是否指向自己线程，这样就少去了加锁步骤的开销，不是的话等持有线程运行到安全点会进行锁的膨胀，在 MarkWord 中会有一个指针指向持有线程栈帧中的对象，虽然有此时再有第三个线程过来或者自旋一定次数仍未获得轻量级锁，此时就要膨胀为重量级锁了，此时指针会指向 monitor 对象，其他线程想获得这个锁会阻塞。

### synchorized 与 ReentrantLock 

- 都是可重入锁
- synchorized 依赖于 JVM，ReentrantLock 依赖于 API
- ReentrantLock 是一个接口，而 synchorized 是关键字
- ReentrantLock 需要显示的加锁和释放锁，synchorized 是自动的
- ReentrantLock 比 synchorized 多了一些功能
  1. 等待可中断；
  1. 可实现公平锁；
  1. 可实现选择性通知。

# 多线程类和接口

## 开启多线程的三种方法

1. 继承 Thread 类

   ```java
   public class Demo {
       public static class MyThread extends Thread {
           @Override
           public void run() {
               System.out.println("MyThread");
           }
       }
   
       public static void main(String[] args) {
           Thread myThread = new MyThread();
           myThread.start();
       }
   }
   ```

1. 实现 Runnable 接口

   ```java
   public class Demo {
       public static class MyThread implements Runnable {
           @Override
           public void run() {
               System.out.println("MyThread");
           }
       }
   
       public static void main(String[] args) {
           new MyThread().start();
   
           // Java 8 函数式编程，可以省略MyThread类
           new Thread(() -> {
               System.out.println("Java 8 匿名内部类");
           }).start();
       }
   }
   ```

1. 实现 Callable 接口，可以有返回值

   `Callable` 提供的方法有返回值，并且支持泛型。

## 线程状态及主要转化方法



![](https://gitee.com/cellophane/image/raw/master/20200421140635.png)

`wait`，`notify`，`notifyAll` 都是 `java.lang.Object` 的方法。

> `wait` 和 `notify` 这一对方法必须在 `synchorized` 方法或者块中调用，只有在 `synchronized` 方法或块中当前线程才占有锁，才有锁可以释放。`notify` 是随机释放的。

1. `wait` 会**让出 CPU 资源**，也就是说 `wait` 线程进入等待池，`cpu` 不分时间片给它，锁释放掉。`wait` 可以指定毫秒单位的时间作为参数，也可以不指定，当超出时间或者调用 `notify()` 方法时，线程进入可执行状态。
1. 当调用 `notify()` 方法后，将从对象的等待池中移走一个任意的线程并放到锁标志等待池中，只有锁标志等待池中的线程能够获取锁标志；如果锁标志等待池中没有线程，则 `notify()` 不起作用。 
1. `notifyAll()` 则从对象等待池中移走所有等待那个对象的线程并放到锁标志等待池中。 

> 释放锁：锁如果被占用，那么这个执行代码片段是同步执行的，如果锁释放掉，就允许其它的线程继续执行此代码块了。 

## sleep、yield、wait、join 的区别

1. `sleep` 是 `Thread` 类的方法，允许指定以毫秒为单位的一段时间作为参数，使得线程在指定的时间进入阻塞状态，**让出 CPU 资源**，指定的时间一过，线程重新进入可执行状态。 `sleep` **不会释放锁**，也就是说如果有 `synchronized` 同步块，其他线程仍然不能访问共享数据，可通过调用 `interrupt()` 方法来唤醒休眠线程。
1. `wait` 是 `Object` 类的方法，必须放在循环体和同步代码块中，执行该方法的线程**会让出 CPU 资源和释放锁**，进入线程等待池中等待被再次唤醒，即放入锁池中竞争同步锁。
1. `yield` 是 `Thread` 类方法，会**让出CPU资源**，不能由用户指定暂停多长时间 ，并且 `yield()` 方法**只能让同优先级的线程**有执行的机会。 `yield()` 只是使当前线程重新回到可执行状态，所以执行 `yield()` 的线程有可能在进入**到可执行状态后**马上又被执行。
1.  `join` 使当前线程等待另一个线程执行完毕之后再继续执行，内部调用的是 `Object` 类的 `wait` 方法实现的。

**最大的不同是在等待时wait会释放锁，而sleep一直持有锁。Wait通常被用于线程间交互，sleep通常被用于暂停执行。**

suspend() 和 resume()：suspend 使得线程进入阻塞状态，不会自动恢复，只有 resume 被调用，才能使得线程重新进入可执行状态。

## start() 和 run()

调用 `start` 方法方可启动线程并使线程进入就绪状态，`start` 内部调用了 `run` 方法，直接调用 run 方法还是在主线程里执行，没有新的线程启动。

## 互斥同步

同步是指多个线程并发访问共享数据时，保证共享数据在同一个时刻只被同一条线程使用。
互斥回实现同步的一种手段，临界区、互斥量和信号量都是主要的互斥实现方式。
因此在这四个字里面，互斥是因，同步是果，互斥是方法，同步是目的。

## 非阻塞同步

互斥同步最主要的问题就是进行线程阻塞和唤醒所带来的性能问题，因此这种同步也被称为阻塞同步（Blocking Synchronization）。另外，它属于一种悲观的并发策略，总是认为只要不去做正确的同步措施（加锁），那就肯定会出现问题，无论共享数据是否真的会出现竞争，它都要进行加锁（这里说的是概念模型，实际上虚拟机会优化掉很大一部分不必要的加锁）、用户态核心态转换、维护锁计数器和检查是否有被阻塞的线程需要被唤醒等操作。随着硬件指令集的发展，我们有了另外一个选择：基于冲突检测的乐观并发策略，通俗地说就是先进行操作，如果没有其他线程争用共享数据，那操作就成功了；如果共享数据有争用，产生了冲突，那就再进行其他的补偿措施（最常见的补偿措施就是不断地重试，直到试成功为止），这种乐观的并发策略的许多实现都不需要把线程挂起，因此这种同步操作被称为非阻塞同步（Non-Blocking Synchronization）。

为什么使用乐观并发策略需要“硬件指令集的发展”才能进行呢？因为我们需要操作和冲突检测这两个步骤具备原子性，靠什么来保证呢？如果这里再使用互斥同步来保证就失去意义了，所以我们只能靠硬件来完成这件事情，硬件保证一个从语义上看起来需要多次操作的行为只通过一条处理器指令就能完成。在 IA64、x86 指令集中通过 cmpxchg 指令完成 CAS 功能。

CAS 指令需要有三个操作数，分别是内存位置（在 Java 中可以简单理解为变量的内存地址，用 V 表示）、旧的预期值（用 A 表示）和新值（用 B 表示）。CAS 指令执行时，当且仅当 V 符合旧预期值 A 时，处理器用新值 B 更新 V 的值，否则它就不执行更新，但是不管是否更新了 V 的值，都会返回 V 的旧值，上述的处理过程是一个原子操作。



# 线程同步的方式

## ReentrantLock

`ReenctrantLock` 和 `synchorized` 很相似，都一样具备线程重入特性，只是代码写法上有点区别，`ReentrantLock` 表现为 API 层面的互斥锁（lock() 和 unlock() 方法配合 try/finally 语句块来完成），一个表现为原生语法层面的互斥锁。不过 `ReentrantLock` 比 `synchronized` 增加了一些高级功能，主要有以下三项：等待可中断、可实现公平锁，以及锁可以绑定多个条件。

1. 等待可中断：持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情，可中断特性对处理执行时间非常长的同步块很有帮助。
1. 可实现公平锁：多个线程在等待同一个锁时，必须按照申请所得时间顺序来依次获得锁，而非公平锁不保证这一点，在锁被释放时，任何一个等待锁的线程都有机会获得锁。`synchronized` 中的锁是非公平的，`ReentrantLock` 默认情况下也是非公平的，但可以通过带布尔值的的构造函数要求使用公平锁。
1. 以及锁可以绑定多个条件：一个 `ReentrantLock` 对象可以同时绑定多个 `Condition` 对象，而在 `synchronized` 中，锁对象的`wait()` 和 `notify()` 或 `notifyAll()` 方法可以实现一个隐含的条件，如果要和多于一个的条件关联的时候，就不得不额外地添加一个锁，而 `ReentrantLock` 则无须这样做，只需要多次调用 `newCondition()` 方法即可。

### 底层原理

reentranklock 的底层是 AQS，而AQS是CLH队列的增强，CLH底层实现了一个虚拟节点的双向链表，每个节点会自旋查询前面一个节点的情况。AQS在此基础上做了增强，支持可重入、可中断、不是一直自旋支持阻塞、支持非公平、支持独占和共享。reentranklock默认是非公平可重入锁。

## ThredLocal

`ThredLocal`一般称为线程本地变量，是一种特殊的线程绑定机制，将变量与线程绑定在一起，为每一个线程维护一个独立的变量副本。通过`ThreadLocal`可以将对象的可见范围限制在同一个线程内。

# 线程池

将资源放入一个池子里，每次使用从里面获取，用完之后放回池子供其他人使用。

线程池的好处：

- 降低资源消耗
- 提示系统响应速度
- 提高线程的可管理性

## 创建方式

- `Executors.newCachedThreadPool()`：无限线程池。
- `Executors.newFixedThreadPool(nThreads)`：创建固定大小的线程池。
- `Executors.newSingleThreadExecutor()`：创建单个线程的线程池。

这三种方式实际上还是利用 `ThreadPoolExecutor` 类实现的。

```java
ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, RejectedExecutionHandler handler)
```

## 核心参数

- `corePoolSize` 线程池的核心线程数。在没有设置 allowCoreThreadTimeOut 为 true 的情况下，核心线程会在线程池中一直存活，即使处于闲置状态。
- `maximumPoolSize` 线程池最大线程大小。当活动线程(核心线程+非核心线程)达到这个数值后，后续任务将会根据 RejectedExecutionHandler 来进行拒绝策略处理。
- `keepAliveTime` 和 `unit` 则是线程空闲后的存活时间。
- `workQueue` 用于存放任务的阻塞队列。
- `handler` 当队列和最大线程池都满了之后的饱和策略。

## 线程池工作原则

- 当线程池中线程数量小于 corePoolSize 则创建线程，并处理请求。
- 当线程池中线程数量大于等于 corePoolSize 时，则把请求放入 workQueue 中,随着线程池中的核心线程们不断执行任务，只要线程池中有空闲的核心线程，线程池就从 workQueue 中取任务并处理。
- 当 workQueue 已存满，放不下新任务时则新建非核心线程入池，并处理请求直到线程数目达到 maximumPoolSize（最大线程数量设置值）。
- 如果线程池中线程数大于 maximumPoolSize 则使用 RejectedExecutionHandler 来进行任务拒绝处理。

## 任务队列 BlockingQueue

任务队列 workQueue 是用于存放不能被及时处理掉的任务的一个队列，它是 一个 BlockingQueue 类型。

> 关于 BlockingQueue，虽然它是 Queue 的子接口，但是它的主要作用并不是容器，而是作为线程同步的工具，他有一个特征，当生产者试图向 BlockingQueue 放入(put)元素，如果队列已满，则该线程被阻塞；当消费者试图从 BlockingQueue 取出(take)元素，如果队列已空，则该线程被阻塞。(From 疯狂Java讲义)

## 拒绝策略

- AbortPolicy 丢弃任务并抛出 RejectedExecutionException 异常（线程池默认）
- DiscardPolicy 丢弃任务但是不抛异常
- DiscardOldestPolicy 丢弃队列最前面的任务，然后重新尝试执行任务
- CallerRunPolicy 重试添加当前的任务，它会自动重复调用execute()方法。

# 阻塞式线程安全队列



# AQS 同步器

AbstractQueuedSynchronizer是纯 Java 实现的。

CAS 

Unsafe 魔法类，可以绕过虚拟机，直接操作底层内存。 swap，atomic 原子操作

Unsafe 不可以直接 new ，要通过反射加载进来。

Unsafe 提供了 park 与 unpark，阻塞和唤醒线程，park 和 unpark 可以通过 LockSupport 调用。 park 和 unpark 是针对某一个线程的，wait 和 notify ，notify 是随机释放。

ConcurrentLinkedQueue 基于 CAS 保证安全

加锁，自旋

同步器核心：自旋 LockSupport CAS

# 参考文章

- [如何优雅的使用和理解线程池](https://crossoverjie.top/2018/07/29/java-senior/ThreadPool/)
- [关于线程池的执行原则及配置参数详解](https://gudong.site/2017/05/03/thread-pool-intro.html)
- [深入浅出Java多线程](https://redspider.gitbook.io/concurrent/)