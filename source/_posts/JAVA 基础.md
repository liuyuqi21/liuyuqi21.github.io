---
title: Java 基础
date: 2020-01-15 11:10:19
tags: Java
categories: Java
---
# 基本类型和包装类型
- 基本类型：byte(8),short(16),int(32),long(64),float(32),double(64),boolean,char(16)
- boolean 可以使用 1 bit来存储，JVM 会在编译时期将 boolean 类型的数据转换为 int。JVM 支持 boolean 数组，但是是通过读写 byte 数组来实现的。
- 基本类型不可以为 null，包装类型可以，所以 POJO 里只能用包装类型，因为数据库得查询结果可能是 null，如果使用基本类型，会抛空指针异常
- 基本类型在栈中直接存储具体数值，而包装类型存储的是堆中的引用
- 引用类型：对象、数组都是引用数据类型。所有引用类型的默认值都是 null。

## 缓存池

- new Integer(123) 每次都会新建一个对象
- Integer.valueOf(123) 会使用缓存池中的对象，多次调用取得同一个对象的引用
- valueOf() 方法会先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容
- 在 Java 8 中，Integer 缓存池的大小默认为 -128~127，但是上界是可调的，在启动 jvm 的时候，通过 `-XX:AutoBoxCacheMax=<size>` 来指定这个缓冲池的大小
- 编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

```java
Integer x = 2;//装箱 调用了 Integer.valueOf(2)
int y = -x;//拆箱 调用了 x.intValue()

Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true

Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

# String
- String 被声明为 final，不可变。
- null 和空字符串不一样

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```

## 不可变的好处
1. 可以缓存 hash 值
    因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。
1. String Pool 的需要
    如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的，才可能使用 String Pool。
1. 线程安全

## String Pool 字符串常量池
字符串常量池保存着所有字符串的字面量，这些字面量在编译时期就确定，可以使用 String 的 intern() 方法在运行过程将字符串添加到 String Pool 中。

当一个字符串调用 intern() 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals() 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个新的字符串，并返回这个新字符串的引用。
```java
String s1 = new String("aaa");
String s2 = new String("aaa");
System.out.println(s1 == s2);// false
String s3 = s1.intern();
String s4 = s1.intern();
System.out.println(s3 == s4);// true

String s5 = "bbb";//会自动创建字面量，自动放入 String Pool 中
String s6 = "bbb";
System.out.println(s5 == s6);// true
```
在 Java 7 之前，String Pool 被放在运行时常量池中，它属于永久代。而在 Java 7，String Pool 被移到堆中。这是因为永久代的空间有限，在大量使用字符串的场景下会导致 OutOfMemoryError 错误。

### new String("abc")
当 String Pool 中还没有 “abc” 字符串对象的时候，new String("abc") 会创建两个字符串对象。
- 编译时期会在 String Pool 中创建一个字符串对象，指向这个 “abc” 字面量。
- 使用 new 方式会在堆中创建一个字符串对象

# 参数传递
Java 的参数使用的是值传递，不是引用传递。在方法中改变对象的字段值会改变原对象该字段值，因为引用的是同一个对象。

# 隐式类型转换
int 比 short 精度高，不能隐式向下转型为 short 类型。但是使用 += 或者 ++ 运算符会执行隐式类型转换。

# 变量

## 局部变量
- 局部变量在栈上分配
- 没有默认值，被声明后，必须经过初始化，才可以使用

## 实例变量
- 声明在一个类中，但在方法，构造方法和语句块之外
- 当一个对象被实例化之后，每个实例变量的值就跟着确定
- 实例变量在对象创建的时候创建，在对象被销毁的时候销毁
- 实例变量的值应该至少被一个方法、构造方法或者语句块引用，使得外部能够通过这些方式获取实例变量信息
- 实例变量可以声明在使用前或者使用后
- 访问修饰符可以修饰实例变量
- 实例变量对于类中的方法、构造方法或者语句块是可见的。一般情况下应该把实例变量设为私有。通过使用访问修饰- 符可以使实例变量对子类可见
- 实例变量具有默认值。数值型变量的默认值是0，布尔型变量的默认值是false，引用类型变量的默认值是null。变量- 的值可以在声明时指定，也可以在构造方法中指定
- 实例变量可以直接通过变量名访问。但在静态方法以及其他类中，就应该使用完全限定名：ObejectReference.VariableName

### 类变量（静态变量）
- 类变量也称为静态变量，在类中以 static 关键字声明，但必须在方法、构造方法和语句块之外。
- 无论一个类创建了多少个对象，类只拥有类变量的一份拷贝。
- 静态变量除了被声明为常量外很少使用。常量是指声明为 public/private，final 和 static 类型的变量。常量初始化后不可改变。
- 静态变量储存在静态存储区。经常被声明为常量，很少单独使用 static 声明变量。
- 静态变量在程序开始时创建，在程序结束时销毁。
- 与实例变量具有相似的可见性。但为了对类的使用者可见，大多数静态变量声明为 public 类型。
- 默认值和实例变量相似。数值型变量默认值是0，布尔型默认值是 false，引用类型默认值是null。变量的值可以在声明的时候指定，也可以在构造方法中指定。此外，静态变量还可以在静态语句块中初始化。
- 静态变量可以通过：ClassName.VariableName的方式访问。
- 类变量被声明为 public static final类型时，类变量名称必须使用大写字母。如果静态变量不是public和final类型，其命名方式与实例变量以及局部变量的命名方式一致。

# 关键字

## final
- 声明数据：对于基本类型，final 使数值不变；对于引用类型， 对象的引用不能改变，但是里面的值可以改变。
- 声明方法不能被子类重写
- 声明类不允许被继承，final 类中的所有成员方法都会被隐式地指定为 final 方法。
- 如果在匿名类或 Lambda 表达式中访问的局部变量，如果不是 final 类型的话，编译器自动加上 final 修饰符。（[【译】为何Lambda中的局部变量必须是final](https://xsinx.com/2019/06/16/为何Lambda中的局部变量必须是final/#5-静态变量和实例变量)）
  - 为什么 Lambda 表达式(匿名类) 不能访问非 final 的局部变量呢？因为实例变量存在堆中，而局部变量是在栈上分配，Lambda 表达(匿名类) 会在另一个线程中执行。如果在线程中要直接访问一个局部变量，可能线程执行时该局部变量已经被销毁了，而 final 类型的局部变量在 Lambda 表达式(匿名类) 中其实是局部变量的一个拷贝。

## static
- **静态变量**：又称为类变量，这个变量属于类，类的所有实例都共享静态变量，可以直接通过类名访问，静态变量在内存中只有一份，存放于 Java 内存区域的**方法区**。
- **静态方法**：静态方法在类加载时就存在，所有静态方法必须有实现，不能是抽象方法。只能访问所属类的**静态字段和静态方法**，方法中不能有 this 和 super 关键字，因此这两个关键字与具体对象关联。
- **静态代码块**：定义在类方法外，在非静态代码块之前执行（静态代码块—>构造代码块—>构造方法），只在类初始化时运行一次
- **静态内部类（static修饰类的话只能修饰内部类）**：静态内部类与非静态内部类之间存在一个最大的区别: 非静态内部类在编译完成之后会隐含地保存着一个引用，该引用是指向创建它的外围类，但是静态内部类却没有。没有这个引用就意味着：
   1. 它的创建是不需要依赖外围类的创建。
   2. 它不能使用任何外围类的非static成员变量和方法。

## super

super 关键字用于从子类访问父类的变量和方法。

- 在构造器中使用`super()`调用父类中的其他构造方法时，该语句必须处于构造器的首行，`this`调用本类中地其他构造方法时，也要放在首行。
- `this`、`super`不能放在`static`方法中。

被 static 修饰的成员属于类，不属于单个这个类的某个对象，被类中所有对象共享。而 this 代表对本类对象的引用，指向本类对象；而 super 代表对父类对象的引用，指向父类对象；所以， **this 和 super 是属于对象范畴的东西，而静态方法是属于类范畴的东西**。

# Object 通用方法

`clone(),equals(),finalize(),getClass(),hashCode(),notify(),notifyAll(),registerNatives(),toString(),wait()`

## equals()

- 对于引用类型 == 判断两个对象的地址是否相等，equals() 判断值是否相等。如果变量为null，调用equals()会报错，用(s!=null&&equals("abc"))或者把非null的对象放在前面"abc".equals(s)
- 在类没有覆盖equals()方法的时候，equals() 等价于 ==

## hashCode()
- hashCode() 返回哈希值，而 equals() 是用来判断两个对象是否等价。等价的两个对象散列值一定相同
- 在覆盖 equals() 方法时应当总是覆盖 hashCode() 方法，保证等价的两个对象哈希值也相等。
- HashSet 和 HashMap 等集合类使用了 hashCode() 方法来计算对象应该存储的位置，因此要将对象添加到这些集合类中，需要让对应的类实现 hashCode() 方法。

## clone()
- clone() 是 Object 的 proceted 方法，一个类不显式去重写 clone()，其他类就不能直接去调用该类实例的 clone() 方法。
- 实现了 clone() 方法的类必须实现 Cloneable 接口，否则会抛出 CloneNotSupportedException 异常

# 面向对象

## 成员变量和局部变量

1. 从语法形式上看:成员变量是属于类的，而局部变量是在方法中定义的变量或是方法的参数；成员变量可以被 public,private,static 等修饰符所修饰，而局部变量不能被访问控制修饰符及 static 所修饰；但是，成员变量和局部变量都能被 final 所修饰。
1. 从变量在内存中的存储方式来看:如果成员变量是使用`static`修饰的，那么这个成员变量是属于类的，如果没有使用`static`修饰，这个成员变量是属于实例的。而对象存在于堆内存，局部变量则存在于栈内存。
1. 从变量在内存中的生存时间上看:成员变量是对象的一部分，它随着对象的创建而存在，而局部变量随着方法的调用而自动消失。
1. 成员变量如果没有被赋初值:则会自动以类型的默认值而赋值（一种情况例外:被 final 修饰的成员变量也必须显式地赋值），而局部变量则不会自动赋值。

## 访问权限
- 默认的，也称为default，在同一包内可见，不使用任何修饰符。
- 私有的，以 private 修饰符指定，在同一类内可见。
- 共有的，以 public 修饰符指定，对所有类可见。
- 受保护的，以 protected 修饰符指定，对同一包内的类和所有子类可见。

## 抽象类

抽象类里可以有抽象方法，如果一个类中包含抽象方法，那么这个类必须声明为抽象类。抽象类不能被实例化，只能被继承。

## 接口

接口不能有任何的方法实现。接口的字段和方法默认是 public 的，并且不允许定义为 private 或者 protected，接口的字段默认是 static 和 final 的。

1. jdk8 之后的接口可以有默认方法,如果同时实现两个接口，接口中定义了一样的默认方法，则必须重写，不然会报错。
1. JDK8 中，接口也可以定义静态方法，可以直接用接口名调用。
1. Jdk 9 在接口中引入了私有方法和私有静态方法。

### 接口和抽象类的区别
- 从设计层面来说，抽象是对类的抽象，提供了一种 IS-A 的关系，是一种模板设计，接口是行为的抽象，是一种 LIKE-A 关系，是一种行为的规范。
- 从使用上来看，一个类可以实现多个接口，但是不能继承多个类
- 接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。
- 接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限。

需要让不相关的类都实现一个方法的时候，可以使用接口。
需要在几个相关的类中共享代码使用抽象类。

## super
如果子类重写了父类的某个方法，可以通过使用 super 关键字来引用父类的方法实现。

## 重写
为了满足里氏替换原则，重写有三个限制
- 子类方法的访问权限必须大于等于父类方法；
- 子类方法的返回类型必须是父类方法返回类型或为其子类型。
- 子类方法抛出的异常类型必须是父类抛出异常类型或为其子类型。

使用 `@Override` 注解，可以让编译器帮忙检查是否满足上面的三个限制条件。

>  构造方法不能被重写，但是可以被重载。

在调用一个方法时，先从本类中查找看是否有对应的方法，如果没有再到父类中查看，看是否从父类继承来。否则就要对参数进行转型，转成父类之后看是否有对应的方法。总的来说，方法调用的优先级为：

- this.func(this)
- super.func(this)
- this.func(super)
- super.func(super)

## 重载
- 存在于同一个类中，指一个方法与已经存在的方法名称上相同，但是**参数类型、个数、顺序**至少有一个不同，**方法返回值和访问修饰符**可以不同。
- 返回值不同，其它都相同不算是重载。

## 面向对象三大特性

### 封装

封装把一个对象的属性私有化，同时提供一些可以被外界访问的属性的方法。

利用抽象数据类型将数据和基于数据的操作封装在一起，使其构成一个不可分割的独立实体。数据被保护在抽象数据类型的内部，尽可能地隐藏内部的细节，只保留一些对外的接口使其与外部发生联系。用户无需关心对象内部的细节，但可以通过对象对外提供的接口来访问该对象。

### 继承

继承实现了 **IS-A** 关系，继承是使用已存在的类的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。通过使用继承我们能够非常方便地复用以前的代码。

### 多态

所谓多态就是指程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编程时并不确定，而是在程序运行期间才确定，即一个引用变量到底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，必须在由程序运行期间才能决定。

## 面向对象和面向过程的区别

- **面向过程** ：**面向过程性能比面向对象高。** 因为类调用时需要实例化，开销比较大，比较消耗资源，所以当性能是最重要的考量因素的时候，比如单片机、嵌入式开发、Linux/Unix 等一般采用面向过程开发。但是，**面向过程没有面向对象易维护、易复用、易扩展。**
- **面向对象** ：**面向对象易维护、易复用、易扩展。** 因为面向对象有封装、继承、多态性的特性，所以可以设计出低耦合的系统，使系统更加灵活、更加易于维护。但是，**面向对象性能比面向过程低**。

# 反射

Java 反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为 Java语言的反射机制。

每个类都有一个 Class 对象，包含了与类有关的信息。当编译一个新类时，会产生一个同名的 .class 文件，该文件内容保存着 Class 对象。

类加载相当于 Class 对象的加载，类在第一次使用时才动态加载到 JVM 中。也可以使用 Class.forName("com.mysql.jdbc.Driver") 这种方式来控制类的加载，该方法会返回一个 Class 对象。

反射可以提供运行时的类信息，并且这个类可以在运行时才加载进来，甚至在编译时期该类的 .class 不存在也可以加载进来。

Class 和 java.lang.reflect 一起对反射提供了支持，java.lang.reflect 类库主要包含了以下三个类：

- Field ：可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段；
- Method ：可以使用 invoke() 方法调用与 Method 对象关联的方法；
- Constructor ：可以用 Constructor 的 newInstance() 创建新的对象。

# 异常
Throwable 可以用来表示任何可以作为异常抛出的类，分为两种： Error 和 Exception。其中 Error 用来表示 JVM 无法处理的错误，Exception 分为两种：
- 受检异常（IOException） ：需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复。
- 非受检异常/运行时异常 （RuntimeException）：是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。

# 泛型
- 泛型就是编写模板代码来适应任意类型
- 不必对类型进行强制转换
- 可以省略编译器能自动推断出的类型 `List<String> list = new ArrayList<>();`
- 不指定泛型参数类型时，编译器会给出警告，且只能将 <T> 视为Object类型
- 泛型不能是基本类型

## 编写泛型
- 编写泛型时，需要定义泛型类型 `<T>`
- 静态方法不能引用泛型类型 `<T>`，必须定义其他类型 `<K>` 来实现泛型
- 泛型可以同时定义多种类型 `<T,K>`

## 擦拭法
- Java 泛型采用擦拭法实现

## extends通配符
- 使用类似 `<? extends Number>` 通配符作为方法参数时表示方法内部可以调用获取 Number 引用的方法，无法传入
- 使用类似 `<T extends Number>` 定义泛型类时表示泛型类型限定为Number或其子类

## super通配符
- 使用类似 `<?super super Integer>` 通配符作为方法参数时表示方法内部可以传入获取 Integer 引用的方法，无法调用
- 使用类似 `<T super Integer>` 定义泛型类时表示泛型类型限定为Integer或其超类

# 多线程

## 有三种实现线程的方法
1. 继承 Thread 类
1. 实现 Runnable 接口
1. 实现 Callable 接口，可以有返回值

推荐使用 Runnable 接口，因为 Java 不支持多继承。
Callable 接口中的 call() 方法有单绘制，是一个泛型，和 Future，FutureTask 配合用来获取异步执行的结果。

## sleep() 和 wait()
- sleep 没有释放锁，wait 释放了锁
- wait 通常被用于线程间的交互/通信，sleep 通常被用于暂停执行
- wait() 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 notify() 或者 notifyAll() 方法。sleep() 方法执行完成后，线程会自动苏醒。或者可以使用 wait(long timeout) 超时后线程会自动苏醒。

## 为什么调用 start() 方法会执行 run() 方法，为什么不能直接调用 run() 方法
调用 start 方法方可启动线程并使线程进入就绪状态，start 内部调用了 run 方法，直接调用 run 方法还是在主线程里执行，没有新的线程启动。

## 守护线程和非守护线程
程序运行完毕，JVM 会等待非守护线程完成后关闭，但是 JVM 不会等待守护线程，比如 GC 线程就是守护线程。

## 什么是多线程上下文切换
多线程的上下文切换是指 CPU 控制权由一个已经正在运行的线程切换到另外一个就绪并等待获取 CPU 执行权的线程的过程。

## 什么导致线程阻塞
- sleep()：sleep 允许指定以毫秒为单位的一段时间作为参数，使得线程在指定的时间进入阻塞状态，不能得到 CPU 时间，指定的时间一过，线程重新进入可执行状态。阻塞时不会释放占用的锁。
- yield()：yield 使当前线程放弃已经分得的 CPU 时间，但不使当前线程阻塞，即线程仍处于可执行状态，随时可能再次分得 CPU 时间。
- suspend() 和 resume()：suspend 使得线程进入阻塞状态，不会自动恢复，只有 resume 被调用，才能使得线程重新进入可执行状态。
- wait() 和 notify()：wait 可以指定毫秒单位的时间作为参数，也可以不指定，当超出时间或者调用 notify 方法时，线程进入可执行状态。这一对方法必须在 synchorized 方法或者块中调用，只有在 synchronized 方法或块中当前线程才占有锁，才有锁可以释放。

## synchronized 和 ReentrantLock
- ReentrantLock 是一个接口，而 synchorized 是关键字
- 都是可重入锁
- ReentrantLock 需要显示的加锁和释放锁，synchorized 是自动的
- ReentrantLock 支持公平锁和非公平锁
- ReentrantLock 可相应中断、可轮回，synchorized 不可以

# IO
- IO 流是一种顺序读写数据的模式
- 二进制数据以 byte 为最小单位在 InputStream/OuputStream 中单向流动（字节流）
- 字符数据以 char 为最小单位在 Reader/Writer 中单向流动
- java.io 包提供了同步 IO 功能

## Java 中 IO 流分为几种

Java IO 流有4个抽象基类:

|        | 字节流       | 字符流 |
| ------ | ------------ | ------ |
| 输出流 | OutPutStream | Writer |
| 输入流 | InputStream  | Reader |
|        |              |        |

![](https://gitee.com/cellophane/image/raw/master/20200411233959.png)

- 按照流的流向分，可以分为输入流和输出流；
- 按照操作单元划分，可以划分为字节流和字符流；
- 按照流的角色划分为节点流和处理流。

在 Java 的 I/O 中使用到装饰器模式,下面以 InputStream 为例：

- InputStream是抽象组件；
- FileInputStream 是 InputStream 的子类，属于具体组件，提供了字节流的输入操作；
- FilterInputStream 属于抽象装饰者，装饰者用于装饰组件，为组件提供额外的功能。例如 BufferedInputStream 为 FileInputStream 提供缓存的功能

在实际开发中，实例化一个具有缓存功能的字节流对象时，只需要在 FileInputStream 对象上再套一层 BufferedInputStream 对象即可，例如：

```java
FileInputStream fileInputStream = new FileInputStream(filePath);
BufferedInputStream bufferedInputStream = new BufferedInputStream(fileInputStream);
```

# Java 与 C++ 的区别
- Java 是纯粹的面向对象语言，所有的对象都继承自 java.lang.Object，C++ 为了兼容 C 即支持面向对象也支持面向过程。
- Java 通过虚拟机从而实现跨平台特性，但是 C++ 依赖于特定的平台。
- Java 没有指针，它的引用可以理解为安全指针，而 C++ 具有和 C 一样的指针。
- Java 支持自动垃圾回收，而 C++ 需要手动回收。
- Java 不支持多重继承，只能通过实现多个接口来达到相同目的，而 C++ 支持多重继承。
- Java 不支持操作符重载，虽然可以对两个 String 对象执行加法运算，但是这是语言内置支持的操作，不属于操作符重载，而 C++ 可以。
- Java 的 goto 是保留字，但是不可用，C++ 可以使用 goto。

# 参考资料
- [Java 基础](https://cyc2018.github.io/CS-Notes/#/notes/Java%20%E5%9F%BA%E7%A1%80)
- [Java 到底是值传递还是引用传递？](https://www.zhihu.com/question/31203609/answer/50992895)
- [bat等大公司常考java多线程面试题](https://juejin.im/post/5b57b81af265da0f4b7a9ae5)
- [为什么阿里巴巴不建议在for循环中使用"+"进行字符串拼接](https://mp.weixin.qq.com/s?__biz=MzAwNDA2OTM1Ng==&mid=2453141826&idx=2&sn=759e6e18c4694b510ac8e0af31608b6c&chksm=8cf2dbc1bb8552d7575b31770cf5af9b25c470f57aa6a9727446ab99ae2d76b2c5767ff280f6&scene=0&xtrack=1#rd)