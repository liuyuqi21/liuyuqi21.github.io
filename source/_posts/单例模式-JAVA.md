---
title: 单例模式 JAVA
date: 2019-09-21 15:23:33
tags: 
    - Java
    - 设计模式
categories: 设计模式
---
目的是在整个系统中只能出现一个类的实例。
- 对于频繁使用的对象，可以省略创建对象所花费的时间
- 由于 new 操作的次数减少，因而对系统内存的使用频率也会降低，这将减轻 GC 压力，缩短 GC 停顿时间
- 有一些对象其实我们只需要一个，比方说：线程池、缓存。这些类对象只能有一个实例。单例模式常常被用来管理共享资源，例如数据库连接或者线程池。

## 懒汉式-线程不安全
私有静态变量 instance 被延迟实例化，这样做的好处是，如果没有用到该类，那么就不会实例化 instance，从而节约资源。

这个实现在多线程环境下不安全，如果有多个线程同时进入 `if (uniqueInstance == null) `，并且此时 uniqueInstance 为 null，那么会有多个线程执行 `uniqueInstance = new Singleton(); `语句，这将导致实例化多次 uniqueInstance。
```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

## 饿汉式-线程安全
直接实例化 instance 就不会产生线程不安全问题。
```java
public class Singleton {
    private static Singleton instance = new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return instance;
    }
}
```

## 懒汉式-线程安全
对 getInstance() 方法加锁。会让线程阻塞时间过长，因此该方法有性能问题，不推荐使用
```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {
    }

    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

## 双重校验锁-线程安全
一定要使用两个 if 语句
使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。
```java
public class Singleton {
    private volatile static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

## 静态内部类
优点：
- 延迟初始化：外部类加载时并不需要立即加载内部类，就不会去初始化 INSTANCE。
- 线程安全：JVM 在执行类的初始化阶段，会获得一个可以同步多个线程对同一个类的初始化的锁

```java
public class Singleton {
    private Singleton() {
    }

    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

## 枚举
可以防止反射攻击
```java
public enum Singleton{
    INSTANCE;

    private String objName;

    public String getObjName() {
        return objName;
    }

    public void setObjName(String objName) {
        this.objName = objName;
    }
}
```