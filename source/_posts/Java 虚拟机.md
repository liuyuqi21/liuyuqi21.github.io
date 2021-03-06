---
title: Java 虚拟机
date: 2020-01-28 22:40:27
tags: Java
categories: Java
---
Java虚拟机（JVM）是运行 Java 字节码的虚拟机。JVM 有针对不同系统的特定实现。目的是使用相同的字节码，他们都会给出相同的结果。

> 在 Java 中，JVM 可以理解的代码就叫做`字节码`（即扩展名为 `.class` 的文件），它不面向任何特定的处理器，只面向虚拟机。Java 语言通过字节码的方式，在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。所以 Java 程序运行时比较高效，而且，由于字节码并不针对一种特定的机器，因此，Java 程序无须重新编译便可在多种不同操作系统的计算机上运行。

JVM 由三个主要的子系统构成

- 类加载子系统
- 运行时数据区（内存结构）
- 执行引擎

JDK 包含 JRE，JRE 包含 JVM，JVM 用来运行 class 文件，对对象的自动管理、内存管理，JDK 多了 Tools，方便你进行 Java 开发，最终还是交给 JVM 运行。

# Java 内存区域（运行时数据区）

![](https://gitee.com/cellophane/image/raw/master/20200408010428.png)

![](https://gitee.com/cellophane/image/raw/master/20200408003517.png)

![](https://gitee.com/cellophane/image/raw/master/20200408003720.png)



![](https://gitee.com/cellophane/image/raw/master/20200407211124.png)

## 程序计数器（Program Counter Register）

程序计数器就是记录当前线程执行程序的位置，改变计数器的值来确定执行的下一条指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。

在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。所以他是**线程私有**的。

程序计数器是唯一一个不会出现 OutOfMemoryError 的内存区域，它的生命周期随着线程的创建而创建，随着线程的结束而死亡。

## Java 虚拟机栈（Stack）
Java 虚拟机栈线程私有的，生命周期和线程相同，创建线程的时候就会创建一个 Java 虚拟机栈。

Java 内存可以粗糙的区分为堆内存（Heap）和栈内存(Stack)，其中栈就是现在说的虚拟机栈。

虚拟机栈描述的是Java方法执行的内存模型：每个方法被执行的时候都会同时创建一个栈帧（Stack Frame）用于存储**局部变量表、操作栈、动态链接、方法出口**等信息。每一个方法被调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程。

**局部变量存放在 Java 虚拟机栈的局部变量表中**。Java8 中基本类型的局部变量的值存放在虚拟机栈的局部变量表中，如果是引用型的变量，则只存储对象的引用地址。

Java 虚拟机栈会出现两种异常：StackOverFlowError 和 OutOfMemoryError（OOM）。

- StackOverFlowError：若 Java 虚拟机栈的内存大小不允许动态扩展，那么当**线程请求栈的深度超过当前 Java 虚拟机栈的最大深度**的时候，就抛出 StackOverFlowError 异常。
- OutOfMemoryError：若 Java 虚拟机栈的内存大小允许动态扩展，且当**线程请求栈时内存用完了**，无法再动态扩展了，此时抛出 OutOfMemoryError 异常。比如当用户请求 web 服务器，每个请求开启一个线程负责用户的响应计算（每个线程分配一个虚拟机栈空间），如果并发量大时，可能会导致内存溢出（OutOfMemoneyError），可以适当的把每个虚拟机栈的大小适当调小一点，减少内存的使用量来提高系统的并发量。

当前栈帧对应方法，没有放对象，但是放了对象的引用。

## 本地方法栈（Native Method Stack）

虚拟机栈为虚拟机执行 Java 方法 （也就是字节码）服务，而本地方法栈则为虚拟机使用到的本地方法（Native）服务。线程在调用本地方法时，来存储本地方法的局部变量表，本地方法的操作数栈等等。

> 本地方法：是非 Java 语言实现的方法，例如 Java 调用 C 语言，来操作某些硬件信息。

线程的 start() 方法是本地方法。

在 Java 虚拟机栈调用本地方法栈是动态链接的一种。 

## 堆（Heap）

![](https://gitee.com/cellophane/image/raw/master/20200407215851.png)

堆里面存放的都是**对象的实例**，几乎所有的对象实例以及数组都在这里分配内存，当对象无法在该空间申请到内存时将抛出 OutOfMemoryEror 异常。Java 堆是垃圾收集器管理的主要区域，因此也被称作**GC堆**（Garbage Collected Heap）。

普通成员变量存储在堆中，因为成员变量随着对象的创建而存在。静态变量存储在方法区中，因为静态变量随着类加载的创建而存在。

堆由年轻代和老年代组成，老年代占堆的2/3空间，而年轻代内存又被分为三部分：Eden 空间、From Survivaor 空间、To Survivor 空间。默认情况下 Eden:From Survivor:To Survivor 按照 8:1:1 的比例来分配。没有直接设置老年代的参数，可以通过堆空间大小和新生代空间大小两个参数来间接控制。
> 老年代空间大小 = 堆空间大小 - 年轻代空间大小

- Eden：新创建的对象放在 Eden 区，如果 Eden 区放不下，就直接放到老年代里面去（分配担保机制）。
- From Survivor 和 To Survivor：保存新生代 gc 后还存活的对象。
- 老年代：对象存活时间比较长（经过多次新生代的垃圾收集，默认是15次）的对象则进入老年的。当堆中分配的对象实例过多，且大部分对象都在使用，就会报内存溢出异常（OutOfMemoneyError）。

新生代 GC 叫做 Young GC/Minor GC，老年代 GC 叫做 Old GC/Major GC，Full GC = Yong GC + Old GC + MetaSpace。

在 JDK 1.8 中移除整个永久代，取而代之的是一个叫元空间（Metaspace）的区域（永久代使用的是 JVM 的堆内存空间，而元空间使用的是物理内存，直接受到本机的物理内存限制）。

## 方法区（Method Area）
方法区存储**已被虚拟机加载**的类信息（构造方法/接口定义）、常量、静态变量、运行时常量池、即时编译器编译后的代码等数据，是线程共享的内存区域。虽然 Java 虚拟机规范把方法区描述为堆的一个逻辑部分，但是它却有一个别名叫做 Non-Heap（非堆），目的应该是与 Java 堆区分开来。（jdk1.8之前hotsopt虚拟机叫永久代/持久代，jdk1.8时叫元空间（MetaSpace））

相对而言，垃圾收集行为在这个区域是比较少出现的，但并非数据进入方法区后就“永久存在”了，方法的垃圾回收主要针对常量池回收、类型卸载（比如反射生成大量的临时使用的Class等信息）。

- 会发生内存溢出

### 运行时常量池（Run-Time Constant Pool）

常量池用于存放**编译期**生成的各种字节码和符号引用，常量池具有一定的动态性，里面可以存放编译期生成的常量；**运行期间**的常量也可以添加进入常量池中，比如 string 的 intern() 方法。

JDK1.7及之后版本的 JVM 已经将运行时常量池从方法区中移了出来，在 Java 堆（Heap）中开辟了一块区域存放运行时常量池。

## 直接内存
直接内存并不是虚拟机运行时数据区的一部分，也不是虚拟机规范中定义的内存区域，但是这部分内存也被频繁地使用。而且也可能导致 OutOfMemoryError 异常出现。

# 垃圾回收

JVM 中，程序计数器、虚拟机栈、本地方法栈都是随线程而生随线程而灭，栈帧随着方法的进入和退出做入栈和出栈操作，实现了自动的内存清理，因此，内存垃圾回收主要集中于**堆和方法区**中，在程序运行期间，这部分内存的分配和使用都是动态的。

![](https://gitee.com/cellophane/image/raw/master/20200408005510.png)

## 对象存活判断
- 引用计数：每个对象有一个引用计数属性，新增一个引用时计数加 1，引用释放时计数减 1，计数为 0 可回收。引用计数简单但无法解决对象相互循环引用的问题，所以 Java 虚拟机不适用引用计数法。

- 可达性分析：从 GC Roots 开始向下搜索，搜索所走过的路径称为引用链。当一个对象到 GC Roots 没有任何引用链相连时，则证明此对象是不可用的，不可达对象。 

  GC Roots 一般包含以下内容：

  - 虚拟机栈中局部变量表中引用的对象
  - 本地方法栈中 JNI 中引用的对象
  - 方法区中类静态属性引用的对象
  - 方法区中的常量引用对象

## 垃圾收集算法

### 标记-清除算法
算法分为“标记”和“清除”两个阶段：首先标记出所有需要回收的对象，在标记完成后统一回收掉所有被标记的对象。

缺点：
- 效率不高
- 产生大量不连续的内存碎片

### 复制算法
它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用过的内存空间一次清理掉。

实现简单，运行高效。代价是将内存缩小为原来的一半，持续复制长生存期的对象导致效率降低。

现在的商业虚拟机都采用这种收集算法回收新生代，但是并不是划分为大小相等的两块，而是一块较大的 Eden 空间和两块较小的 Survivor 空间，每次使用 Eden 和其中一块 Survivor。在回收时，将 Eden 和 Survivor 中还存活着的对象全部复制到另一块 Survivor 上，最后清理 Eden 和使用过的那一块 Survivor。

HotSpot 虚拟机的 Eden 和 Survivor 大小比例默认为 8:1，保证了内存的利用率达到 90%。如果每次回收有多于 10% 的对象存活，那么一块 Survivor 就不够用了，此时需要依赖于老年代进行空间分配担保，也就是借用老年代的空间存储放不下的对象。

### 标记-整理算法
让所有存活对象都向一端移动，然后直接清理掉边界以外的内存。
- 优点：不会产生内存碎片
- 缺点：需要移动大量对象，处理效率比较低

### 分代收集算法

现在的商业虚拟机采用分代收集算法，它根据对象存活周期将内存划分为几块，不同块采用适当的收集算法。

一般将堆分为新生代和老年代。

- 在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。
- 而老年代中因为对象存活率高、没有额外空间对它进行分配担保，就必须使用“**标记-清理**”或“**标记-整理**”算法来进行回收。

## 垃圾收集器

![](https://gitee.com/cellophane/image/raw/master/20200408010311.png)

### Serial 收集器

以串行的方式执行，只使用一个线程去回收，垃圾收集的过程中会 Stop The World（服务暂停）。是新生代收集器，使用 **复制** 算法。

它的优点是简单高效，在单个 CPU 环境下，由于没有线程交互的开销，因此拥有最高的单线程收集效率。

它是 Client 场景下的默认新生代收集器，因为在该场景下内存一般来说不会很大。它收集一两百兆垃圾的停顿时间可以控制在一百多毫秒以内，只要不是太频繁，这点停顿时间是可以接受的。

### ParNew 收集器
它是 Serial 收集器的多线程版本。它是 Server 场景下默认的新生代收集器，除了性能原因外，主要是因为除了 Serial 收集器，只有它能与 CMS 收集器配合使用。

### Parallel Scavenge 收集器（1.8默认）
是多线程收集器（并行），采用**复制**算法。

其它收集器目标是尽可能缩短垃圾收集时用户线程的停顿时间，而它的目的是达到一个可控制的吞吐量，因此它被称为“吞吐量优先”收集器。这里的吞吐量指 CPU 用于运行用户程序的时间占总时间的比值。

### Parallel Old 收集器

是 Parallel Scavenge 收集器的老年代版本，使用多线程和**标记－整理**算法。

在注重吞吐量以及 CPU 资源敏感的场合，都可以优先考虑 Parallel Scavenge 加 Parallel Old 收集器。

### Serial Old 收集器

是 Serial 收集器的老年代版本，算法使用的是 **标记-整理** 也是给 Client 场景下的虚拟机使用。如果用在 Server 场景下，它有两大用途：

- 在 JDK 1.5 以及之前版本（Parallel Old 诞生以前）中与 Parallel Scavenge 收集器搭配使用。
- 作为 CMS 收集器的后备预案，在并发收集发生 Concurrent Mode Failure 时使用。

### CMS 收集器
CMS（Concurrent Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器，适用于 **老年代**。Mark Sweep 指的是 **标记-清除** 算法。

分为以下四个流程：
- 初始标记：仅仅只是标记一下 GC Roots 能直接关联到的对象，速度很快，需要停顿。

- 并发标记：进行 GC Roots Tracing 的过程，它在整个回收过程中耗时最长，不需要停顿。

- 重新标记：为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，需要停顿。

- 并发清除：不需要停顿。

在整个过程中耗时最长的并发标记和并发清除过程中，收集器线程都可以与用户线程一起工作，不需要进行停顿。

- 优点：并发收集，低停顿
- 缺点：
  - 吞吐量低：低停顿时间是以牺牲吞吐量为代价的，导致 CPU 利用率不够高。
  - 无法处理浮动垃圾，可能出现 Concurrent Mode Failure。浮动垃圾是指并发清除阶段由于用户线程继续运行而产生的垃圾，这部分垃圾只能到下一次 GC 时才能进行回收。由于浮动垃圾的存在，因此需要预留出一部分内存，意味着 CMS 收集不能像其它收集器那样等待老年代快满的时候再回收。如果预留的内存不够存放浮动垃圾，就会出现 Concurrent Mode Failure，这时虚拟机将临时启用 Serial Old 来替代 CMS。
  - 标记 - 清除算法导致的空间碎片，往往出现老年代空间剩余，但无法找到足够大连续空间来分配当前对象，不得不提前触发一次 Full GC。

### G1 收集器

G1 是目前技术发展的最前沿成果之一，HotSpot 开发团队赋予它的使命是未来可以替换掉 JDK1.5 中发布的 CMS 收集器。

G1 直接对新生代和老年代一起回收，把堆划分为多个大小相等的独立区域（Region），新生代和老年代不再物理隔离。

具备以下特点：
- 空间整合：整体来看是基于“标记 - 整理”算法实现的收集器，从局部（两个 Region 之间）上来看是基于“复制”算法实现的，这意味着运行期间不会产生内存空间碎片。
- 可预测的停顿：能让使用者明确指定在一个长度为 M 毫秒的时间片段内，消耗在 GC 上的时间不得超过 N 毫秒。

## 内存分配策略与回收策略



# 类加载机制

![](https://gitee.com/cellophane/image/raw/master/classload.png)

![](https://gitee.com/cellophane/image/raw/master/20200407201734.png)

![](https://gitee.com/cellophane/image/raw/master/20200407232110.png)

类从被加载到虚拟机内存中开始，到卸载出内存为止，它的整个生命周期包括：加载-验证-准备-解析-初始化-使用-卸载七个阶段。其中**验证、准备、解析**三个部分统称为**连接**。

准备：给静态变量赋初始值，比如`public static int a = 10`，在准备的时候赋值为 0，初始化的时候才赋值为 10。

解析：符号引用转化为直接引用。

ClassLoader 类加载器

![](https://gitee.com/cellophane/image/raw/master/20200407233339.png)

## 加载

- 通过类的完全限定名称获取定义该类的二进制字节流
- 将该字节流表示的静态存储结构转换为方法去的运行时存储结构
- 在内存中生成一个代表该类的 class 对象，作为方法区中该类各种数据的访问入口 

## 双亲委派模型
如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托到父加载器去完成，依此向上，因此，所有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜索范围中没有找到所需的类时，即无法完成该加载，子加载器才会尝试自己去加载该类。

意义：
- 系统类防止内存中出现多份同样的字节码，同样全限定名的类只加载一次
- 保证 Java 程序安全稳定运行

全盘负责委托机制

### 如何破坏双亲委派



## 类加载器的种类

启动类加载器

扩展类加载器

系统类加载器

用户自定义加载器

# Java 内存模型
> cpu 的处理速度远快于内存的读写速度，因此 Java 采用高速缓存建立其桥梁。

![](https://gitee.com/cellophane/image/raw/master/20200409012349.png)

![](https://gitee.com/cellophane/image/raw/master/20200409112634.png)

Java 内存模型规范数抽象的概念，描述的是程序间变量的访问规则（多线程程序允许表现出的行为），Java 线程内存模型与 CPU 缓存模型类似，它是标准化的，用于屏蔽掉各种硬件和操作系统内存访问差异。

在 JDK1.2 之前，Java 的内存模型实现总是从**主存**（即共享内存）读取变量，是不需要进行特别的注意的。而在当前的 Java 内存模型下，线程可以把变量保存**本地内存**（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成**数据的不一致**。

![](https://gitee.com/cellophane/image/raw/master/数据不一致.png)

为了保证共享内存的正确性（可见性、有序性、原子性），内存模型定义了共享内存系统中多线程程序读写操作行为的规范。

Java 内存模型（Java Memory Model，JMM）就是一种符合内存模型规范的，屏蔽了各种硬件和操作系统的访问差异的，保证了 Java 程序在各种平台下对内存的访问都能保证效果一致的机制及规范。
目的是解决由于多线程通过共享内存进行通信时，存在的本地内存数据不一致、编译器会对代码指令重排序、处理器会对代码乱序执行等带来的问题。

1. Java 内存模型规定了所有的变量都存储在主内存中，每条线程还有自己的工作内存
1. 线程的工作内存中保存了该线程中是用到的变量的主内存副本拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存。
1. 不同的线程之间无法直接访问对方工作内存中的变量
1. 线程间变量的传递均需要自己的工作内存和主存之间进行数据同步进行

## volatile
当一个变量被定义成 `volatile` 后，它具备两种特性
1. 可见性：当一条线程修改了这个变量的值，新值对于其他线程来说是可以立即得知的。

volatile 变量在各个线程的工作内存中不存在一致性问题（在各个线程的工作内存中 volatile 变量也可以存在不一致的情况，但由于每次使用之前都要先刷新，执行引擎看不到不一致的情况，因此可以认为不存在一致性问题），但是 Java 里面的**运算并非原子操作**，导致 volatile 变量的运算在并发下一样是不安全的。

示例：
```java
public static volatile int race = 0;
public static void increase(){
    race++;
}
```
如果发起 20 个线程，每个线程对 race 变量进行 10000 次自增操作，如果这段代码能够正确并发的话，最后输出的结果应该是200000。但是实际上每次运行程序，输出的结果都不一样，都是一个小于 200000 的数字。

因为自增运算 `race++` 不是原子的。导致 `volatile` 变量的**运算**在并发下一样是不安全的，仍然要通过 `synchorized` 加锁来保证原子性。

volatile 的使用场景：下面的代码能保证当 shutdown() 方法被调用时，能保证所有线程中执行的 doWork() 方法都立即停下来。
```java
volatile boolean shutdownRequested;

public void shutdown(){
    shutdownRequested = true;
}

public void doWork(){
    while(!shutdownRequested){
        //do stuff
    }
}
```
2. 禁止指令重排序

## synchorized
synchorized 块之间的操作具备原子性。

## 原子性
1000 个线程同时对 num 执行自增的操作，得到的结果可能是 980，因为一个线程执行自增过程中，另一个线程也执行了自增，两个线程同时写入了主内存。
解决方式：
- 原子类（Atomic）：使用 `AutomicInteger` 来保证原子性

```java
private AtomicInteger num = new AtomicInteger();
```
- 互斥锁：`synchronized` 用来保证方法和代码块内的操作是原子性的。

## 可见性
在下列情况下，线程 B 读取的值可能不是线程 A 写入的最新值。
- 执行线程 A 的处理器把变量 V 缓存到寄存器中
- 执行线程 A 的处理器吧变量 V 缓存到自己的缓存中，但还没有同步刷新到主内存中去
- 执行线程 B 的处理器的缓存中有变量 V 的旧值

解决方式：
- 使用 `volatile` 来保证多线程操作时变量的可见性。被其修饰的变量在被修改后可以立即同步到主内存，在每次是用之前都从主内存刷新。
- 使用 `synchorized` 加锁，同步块的可见性是由“对一个变量执行 unlock 操作之前，必须先把此变量同步回主内存”这条规则获得的。
- 使用 `final` 关键字。

## 有序性
无序是因为**指令重排**。单线程下提高代码效率，但会影响多线程并发执行的正确性。

指令重排：
- 编译器生成指令的次序，可以不同于源代码的版本。
- 处理器可以乱序或者并行的执行指令。
- 缓存会改变写入提交到主内存的变量的次序。

解决方式：
- `volatile` 关键字会禁止指令重排
- `synchronized` 关键字保证同一时刻只允许一条线程操作。

# JVM 性能调优工具

## jps

查看正在运行的 java 程序，可以查看到死锁。

![](https://gitee.com/cellophane/image/raw/master/20200407225324.png)

## jinfo 

查看正在运行的 Java 程序的扩展

## jstat 

可以查看堆内存各部分的使用量，以及加载类的数量。

`jstat [-命令选项] [vmid] [间隔时间/毫秒] [查询次数]`

## jmap 

堆内存 dump，可以在设置内存溢出的时候自动导出 dump 文件。在内存溢出的时候，拿到这个文件进行分析，比如使用 jvisualvm。

1. -XX:+HeapDumpOnOutOfMemoryError
1. -XX:HeapDumpPath=输出路径

## jstack



# 参考文章

- 图基本来自网上视频（鲁班、咕泡、图灵等，b站有）
- [JVM 系列文章](http://www.ityouknow.com/java.html)
- [Java 虚拟机](https://cyc2018.github.io/CS-Notes/#/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA)
- [再有人问你 Java 内存模型是什么，就把这篇文章发给他](https://juejin.im/post/5dd627efe51d4536be153b7a)
- [探索 ConcurrentHashMap 高并发性的实现机制](https://www.ibm.com/developerworks/cn/java/java-lo-concurrenthashmap/index.html)
- [关于Java内存模型的三个特性](https://juejin.im/post/5cb5d419e51d456e500f7d02)