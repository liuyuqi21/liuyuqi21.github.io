---
title: IOC 与 AOP
date: 2020-03-08 21:46:44
tags: spring
catrgories: 设计模式
---
# IOC
IOC 的实现方式是依赖注入。

依赖注入主要有以下实现方式：
- 基于 set 方法： 实现特定属性的 public set() 方法,来让 IoC 容器调用注入所依赖类型的对象。
- 基于接口: 实现特定接口以供 IoC 容器注入所依赖类型的对象。
- 基于构造函数: 实现特定参数的构造函数,在创建对象时来让 Io C容器注入所依赖类型的对象。
- 基于注解: 通过 Java 的注解机制来让 IoC 容器注入所依赖类型的对象,例如 Spring 框架中的 `@Autowired`。

## IOC 思想
传统实现中，调用一个类的方法需要先实例化。
```java
public class Person{
    public void cook(){
        Food food = new Tomato();
        System.out.println("cooking " + food.getName());
    }
}
```
而 IOC 是通过一个第三方容器来管理并维护这些对象，应用程序只需要接收并使用 IOC 容器注入的对象而不需要关注其他事情。控制反转其实就是对象控制权的转移,应用程序将对象的控制权转移给了第三方容器并通过它来管理这些被依赖对象,完成了应用程序与被依赖对象的解耦。
```java
public class Person {
	private Food food;

	public void setFood(Food food) {
		this.food = food;
	}

	public void cook(){
        System.out.println("cooking " + this.food.getName());
    }
}
```

# AOP 
AOP(Aspect-Oriented Programming) 即面向切面编程。它是一种在运行时,动态地将代码切入到类的指定方法、指定位置上的编程思想。用于切入到指定类指定方法的代码片段叫做切面,而切入到哪些类中的哪些方法叫做切入点。

AOP解决了代码的重复并将这些外围业务代码抽离到一个切面中,我们可以动态地将切面切入到切入点。

# 循环依赖问题
A 依赖 B，B 依赖 C，C 依赖 A，Spring 实例化 A 的时候发现 A 依赖于 B 于是去实例化 B（此时 A 创建未结束，处于创建中的状态），而发现 B 又依赖于 A ，于是就这样循环下去，最终导致 OOM。

Spring 为了解决单例的循环依赖问题，使用了 三级缓存，递归第哦啊用时发现 Bean 还在创建中即为循环依赖。

1. A 创建过程中需要 B，于是 A 将自己放到三级缓里面 ，去实例化 B
1. B 实例化的时候发现需要 A，于是 B 先查一级缓存，没有，再查二级缓存，还是没有，再查三级缓存，找到了！
    1. 然后把三级缓存里面的这个 A 放到二级缓存里面，并删除三级缓存里面的 A
    1. B 顺利初始化完毕，将自己放到一级缓存里面（此时B里面的A依然是创建中状态）
1. 然后回来接着创建 A，此时 B 已经创建结束，直接从一级缓存里面拿到 B ，然后完成创建，并将自己放到一级缓存里面
1. 如此一来便解决了循环依赖的问题

# 参考文章
- [IoC与AOP的那点事儿](http://blog.didispace.com/spring-ioc-aop/)

