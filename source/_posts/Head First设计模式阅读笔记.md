---
title: Head First设计模式阅读笔记
date: 2020-02-19 14:06:37
tags: 读书笔记
categories: 设计模式
---
# 设计原则
- 找出应用中可能需要变化之处，把他们独立出来，不要和那些不需要变化的代码混在一起。
- 针对接口编程，而不是针对实现编程。
- 多用组合，少用继承
- 为了交互对象之间的松耦合设计而努力
- 类应该对扩展开放，对修改关闭
- 依赖倒置原则：要依赖抽象，不要依赖具体类

# 策略模式
定义：策略模式定义了算法族，分别封装起来，让它们之间可以相互替换，此模式让算法的变化独立于使用算法的客户。

例子：有一个鸭子模拟游戏，要让鸭子有一个起飞的功能，假如在 Duck 父类上面加一个 fly() 方法，所有鸭子都会继承 fly() 方法，但是橡皮鸭不会飞，所以要把飞行行为从 Duck 类中抽取出来，分开变化和不会变化的部分。
Duck 类
```java
public abstract class Duck {
    // 为行为接口类型声明两个引用变量，所有鸭子子类都继承它们
    FlyBehavior FlyBehavior;
    QuackBehavior QuackBehavior;

    public Duck() {

    }

    // 委托给行为类
    public void performFly() {
        FlyBehavior.fly();
    }

    public void performQuack() {
        QuackBehavior.quack();
    }

    public void swim
    {
        System.out.println("All ducks float, even decoys");
    }
}
```
FlyBehavior 接口
```java
//所有飞行行为类必须实现的接口
public interface FlyBehavior {
    public void fly();
}

// 飞行行为实现，给真的会飞的鸭子用
public class FlyWithWings implements FlyBehavior {
    public void fly() {
        System.out.println("I'm flying!!");
    }
}

// 给不会飞的鸭子用（橡皮鸭）
public class FlyNoWay implements FlyBehavior {
    public void fly() {
        System.out.printn("I can't fly");
    }
}
```
QuackBehavior 类
```java
public interface QuackBehavior {
    public void quack();
}


// 呱呱叫
public class Quack implements QuackBehavior {
    public void quack() {
        System.out.println("Quack");
    }
}

// 不会叫
public class MuteQuack implements QuackBehavior {
    public void quack() {
        System.out.printn("<< Silence >>");
    }
}

// 吱吱叫
public class Squeak implements QuackBehavior {
    public void quack() {
        System.out.printn("squeak");
    }
}
```
ModelDuck 类
```java
public class ModelDuck extends Duck {
    public ModelDuck() {
        // 设置 flyBehavior 和 quackBehavior 的实例变量
        quackBehavior = new Quack();
        flyBehavior = new FlyNoWay();
    }
}
```
测试类
```java
public class MiniDuckSimulator {
    public static void main(String[] args) {
        Duck model = new ModelDuck();
        model.performQuack();
        model.performFly();
    }
}
```
## 动态设定行为
可以在鸭子子类中通过“设定方法”来设定鸭子的行为，为u不是在鸭子构造期内实例化。
1. 在 Duck 类中，加入新方法

```java
public void setFlyBehavior(flyBehavior fb){
    flyBehavior(fb);
}
```

2. 改变测试类，设定行为

```java
public class MiniDuckSimulator {
    public static void main(String[] args) {
        Duck model = new ModelDuck();
        model.performQuack();
        model.performFly();
        //使本来不会飞的模型鸭可以飞了
        model.setFlyBehavior(new FlyWithWings());
        model.performFly();
    }
}
```

# 观察者模式
定义：观察者模式定义了对象之间一对多依赖，这样一来，当一个对象改变状态使，它的所有依赖着都会收到通知并更新。
![](https://gitee.com/cellophane/image/raw/master/observer.png)例子：从气象站拿到三个测量值，温度、湿度与气压，当有新的测量数据时，三个新的布告板要更新，分别是“目前状况”、“气象统计”、“天气预报”。

```java
public interface Subject {
    // 这两个方法都需要一个观察者作为变量，该观察者是用来注册或被删除的
    public void registerObserver(Observer o);

    public void removeObserver(Observer o);

    //当主题状态改变时，这个方法会被调用，以通知所有的观察者
    public void notifyObservers();
}

public interface Observer {
    //所有观察者都必须实现 update() 方法，以实现观察者接口
    public void update(float temp, float humidity, float pressure);
}

public interface DisplayElement {
    //当布告板需要显示时，调用此方法
    public void display();
}
```
实现观察者接口
```java
public class WeatherData implements Subject {
    //使用一个 ArrayList 来记录观察者
    private ArrayList observers;
    private float temperature;
    private float humidity;
    private float pressure;

    public WeatherData() {
        observers = new ArrayList();
    }

    //注册观察者
    public void registerObserver(Observer o) {
        observers.add(o);
    }

    //取消注册
    public void removeObserver(Observer o) {
        int i = observers.indexOf(o);
        if (i >= 0) {
            observers.remove(i);
        }
    }

    public void notifyObservers() {
        for (Observer o : observers) {
            o.update(temperature, humidity, pressure);
        }
    }

    public void measurementsChanged() {
        notifyObservers();
    }

    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }
}
```
目前状况布告板
```java
public class CurrentConditionsDisplay implements Observer, DisplayElement {
    private float temperature;
    private float humidity;
    private Subject weatherData;

    //构造器需要 weatherData 对象作为注册之用
    public CurrentConditionsDisplay(Subject weatherData) {
        this.weatherData = weatherData;
        weatherData.registerObserver(this);
    }

    //当 update 被调用时，我们把温度和湿度保存起来，然后调用 display()
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        display();
    }

    public void display() {
        System.out.println("Current conditions: " + temperature + "F degrees and " + humidity + "% humidity");
    }
}
```

## 推与拉模式
上面的代码是推模式的实现，推模式的主题将所有数据推给观察者，有时候观察者并不需要所有数据，如果采用拉模式，就是由观察者自己去取得所需要的数据，而且这样在扩展功能时主题就不用修改和更新对每位观察者的调用，只需改变自己来允许更多的 getter 方法来取得新增的状态。

## 使用 Java 内置的观察者模式
java.util 包内包含 Observer 接口与 Observerable 类，同时支持“推”和“拉”。

可观察者如何发送通知：
- 先调用 setChanged() 来标记状态已经改变。
- 然后调用 `notifyObservers()`（拉模式）或 `notifyObservers(Object arg)`（推模式，当通知时，可以传送任何数据对象给每一个观察者）。

> setChanged() 方法可以让你在更新观察者时有更多的弹性，可以更适当的通知观察者，比如气象站测量的数据每十分之一度就会更新，如果我们希望半度以上才更新，可以在温度差距达到半度时，调用 setChanged(),进行有效的更新。

观察者如何接收通知：
- 调用 `update(Observable o, Object arg)`，第一个变量时是可观察者，让观察者知道是哪个主题通知它的，第二个变量正是传入 `notifyObservers()` 的数据对象，如果没有说明则为空。

利用内助的观察者模式重做
```java
import java.util.Observable;
import java.util.Observer;

//继承 Observable，注册与删除在超类中。
public class WeatherData extends Observable {
    private float temperature;
    private float humidity;
    private float pressure;

    public WeatherData() {
    }

    public void measurementsChanged() {
        //表示状态以改变
        setChanged();
        //notifyObservers() 中没有传送数据对象，表示采用拉模型
        notifyObservers();
    }

    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }

    //由于我们采用拉模型，所以观察者会利用这些方法取得 WeatherData 对象的状态
    public float getTemperature() {
        return temperature;
    }

    public float getHumidity() {
        return humidity;
    }

    public float getPressure() {
        return pressure;
    }

}

//目前状况布告板
public class CurrentConditionsDisplay implements Observer, DisplayElement {
    Observable observable;
    private float temperature;
    private float humidity;

    //注册观察者
    public CurrentConditionsDisplay(Observable obs) {
        this.observable = obs;
        obs.addObserver(this);
    }

    //先确定可观察者属于 WeatherData 类，然后利用 getter 方法获取温度和湿度
    public void update(Observable obs, Object arg) {
        if (obs instanceof WeatherData) {
            WeatherData weatherData = (WeatherData) obs;
            this.temperature = temperature;
            this.humidity = humidity;
            display();
        }
    }

    public void display() {
        System.out.println("Current conditions: " + temperature + "F degrees and " + humidity + "% humidity");
    }
}
```

## java.util.Observable 的黑暗面
1. Observable 是一个类而不是接口，所以必须设计一个类来继承它，如果某类相同时具有 Observable 和另一个超类的行为，就会陷入两难，毕竟 Java 不支持多重继承。这限制了 Observable 的复用潜力。
1. 因为没有 Observable 接口，就无法建立自己的实现，也无法将它的实现换成另一套做法的实现。

# 装饰者模式
定义：装饰者模式动态地将责任附加到对象上，若要扩展功能，装饰者提供了比继承更有弹性的替代方案。
- 装饰者和被装饰者有相同的超类型
- 可以用一个或者多个装饰者包装一个对象
- 既然装饰者和被装饰者有相同的超类型，所以在任何需要原始对象的场合，可以用装饰过的对象代替它
- **装饰者可以在所委托被装饰者的行为之前/或之后，加上自己的行为，以达到特定的目的**

例子：饮料需要加入各种不同的调料，还要根据加入的调料收取不同的费用。如果利用继承，就会出现类数量爆炸、设计死板，以及基类加入的新功能并不适用于所有的子类的问题。所以，这里要采用不一样的做法：我们要以饮料为主体，然后在运行时加以调料来“装饰”饮料，比如顾客要摩卡和奶泡深焙咖啡。
1. 拿一个深焙咖啡对象
1. 以摩卡对象装饰它
1. 以奶泡对象装饰它
1. 调用 cost() 方法，并依赖委托将调料的价钱加上去

饮料类
```java
public abstract class Beverage {
    String description = "Unknown Beverage";

    public String getDescription() {
        return description;
    }

    public abstract double cost();
}
```
调料类
```java
public abstract class CondimentDecorator extends Beverage {
    public abstract String getDescription();
}

public class Espresso extends Beverage {
    public Espresso() {
        description = "Espresso"
    }

    public double cost() {
        return 1.99;
    }
}
```
摩卡和奶泡类
```java
public class Mocha extends CondimentDecorator {
    Beverage beverage;

    public Mocha(Beverage beverage) {
        this.beverage = beverage;
    }

    public String getDescription() {
        return beverage.getDescription() + ", Mocha";
    }

    public double cost() {
        return 0.20 + beverage.cost();
    }
}

public class Whip extends CondimentDecorator {
    Beverage beverage;

    public Mocha(Beverage beverage) {
        this.beverage = beverage;
    }

    public String getDescription() {
        return beverage.getDescription() + ", Whip";
    }

    public double cost() {
        return 0.30 + beverage.cost();
    }
}
```
测试类
```java
public class Coffee {
    public static void main(String args[]) {
        Beverage beverage = new Espresso();
        System.out.println(beverage.getDescription() + " $" + beverage.cost());

        beverage = new Mocha(beverage);
        beverage = new Whip(beverage);
        System.out.println(beverage.getDescription() + " $" + beverage.cost());
    }
}
```
Java I/O 流使用了装饰者模式。

# 工厂模式
假如你有一个披萨店，有很多类型的披萨。
```java
Pizza OrderPizza(String type) {
    Pizza pizza;

    if (type.equals("cheese")) {
        pizza = new CheesePizza();
    } else if (type.equals("greek")) {
        pizza = new GreekPizza();
    } else if (type.equals("pepperoni")) {
        pizza = new PepperoniPizza();
    }

    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();

    return pizza;
}
```
如果要增加或者修改某些类型的披萨，就要修改上面的代码，这个代码没有对修改封闭，这些是会变化的部分。

## 简单工厂
封装创建对象的代码，将创建披萨的代码移到另一个对象中，由这个新对象专职创建披萨。我们称这个新对象为“工厂”。
```java
public class SimplePizzaFactory {
    public Pizza CreatePizza(String type) {
        Pizza pizza;

        if (type.equals("cheese")) {
            pizza = new CheesePizza();
        } else if (type.equals("greek")) {
            pizza = new GreekPizza();
        } else if (type.equals("pepperoni")) {
            pizza = new PepperoniPizza();
        }
        return pizza;
    }
}
```
这么做有什么好处？
- SimplePizzaFactory 可以有许多的客户，虽然现在只看到 orderPizze() 方法是它的客户，然而可能还有菜单类会利用这个工厂来取得披萨的价钱和描述，还可能有一个宅急送类。

简单工厂其实不是一个设计模式，反而比较像是一种编程习惯。

## 工厂方法
定义：工厂方法模式定义了一个创建对象的接口，但由子类决定要实例化的类是哪一个。工厂方法让类把实例化推迟到子类。
![](https://gitee.com/cellophane/image/raw/master/factory.png)
通过子类来创建对象，客户只需要他们所使用的抽象类型就可以了，而由字类负责决定具体类型，负责将客户从具体类型解耦。

使用工厂方法改写上面的例子（与书中不同）
```java
public abstract class PizzaStore {
    public Pizza orderPizza() {
        Pizza pizza;

        pizza = createPizza();

        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();

        return pizza;
    }

    //工厂方法是抽象的，所以依赖子类来处理对象的创建
    protected abstract Pizza createPizza();
}
```
生产 CheesePizza 的工厂
```java
public class CheeseFactory extends PizzaStore {
    public Pizza createPizza() {
        return new CheesePizza();
    }
}
```
生产 GreekPizza 的工厂
```java
public class GreekFactory extends PizzaStore {
    public Pizza createPizza() {
        return new GreekPizza();
    }
}
```
Pizza 类省略

测试类
```java
public class PizzaTestDrive {
    public static void main(String[] args) {
        PizzaStore cheeseFactory = new CheeseFactory();
        PizzaStore greekFactory = new GreekFactory();

        Pizza pizza = cheeseFactory.orderPizza();
        System.out.println("order a " + pizza.getName() + "\n");
        pizza = greekFactory.orderPizza();
        System.out.println("order a " + pizza.getName() + "\n");
    }
}
```

## 抽象工厂
定义：抽象工厂模式提供一个接口，用于创建相关或依赖对象的家族，而不需要明确指定具体类。
![](https://gitee.com/cellophane/image/raw/master/abstractFac.png)提供一个用来创建一个**产品家族**的抽象类型，这个类型的子类定义了产品被产生的方法。要想使用这个工厂，必须先实例化它，然后将它传入一些针对抽象类型所写的代码中。可以把客户从所使用的实际具体产品中解耦。

可以把一群相关的产品集合起来。

例子：不同地区的加盟店会用到不同的原料，我们需要建造一个工厂来生产原料。

先为工厂定义一个接口，负责创建所有原料
```java
public interface PizzaIngredientFactory {
    //所有原料都是一个类
    public Dough createDough();

    public Sauce createSauce();

    public Cheese createCheese();
}
```
创建纽约原料工厂
```java
public class NYIngredientFactory implements PizzaIngredientFactory {
    //对于每一种原料，都提供了纽约版本
    public Dough createDough() {
        return new ThinCrustDough();
    }

    public Sauce createSauce() {
        return new MarinaraSauce();
    }

    public Cheese createCheese() {
        return new ReggianoCheese();
    }
}
```
披萨类
```java
public abstract class Pizza {
    Stirng name;
    Dough dough;
    Sauce sauce;
    Cheese cheese;

    abstract void prepare();

    void bake() {
        //省略
    }

    void cut() {
    }

    void box() {
    }
}
```
CheesePizza 类
```java
public class CheesePizza extends Pizza {
    PizzaIngredientFactory ingredientFactory;

    public CheesePizza(PizzaIngredientFactory ingredientFactory) {
        this.ingredientFactory = ingredientFactory;
    }

    void prepare() {
        dough = ingredientFactory.createDough();
        sauce = ingredientFactory.createSauce();
        cheese = ingredientFactory.createCheese();
    }
}
//ClamPizza 省略
```
纽约加盟店
```java
public class NYPizzaStore extends PizzaStore {
    protected Pizza createPizza(String item) {
        Pizza pizza = null;
        PizzaIngredientFactory ingredientFactory = new NYIngredientFactory();
        if (item.equals("cheese")) {
            pizza = new CheesePizza(ingredientFactory);
        } else if (item.equals("clam")) {
            pizza = new ClamPizza(ingredientFactory);
        }

        return pizza;
    }
}
```
工厂方法潜伏在抽象工厂里。原料类中有工厂方法。

# 单例模式
定义：单例模式确保一个类只有一个实例，并提供一个全局访问点。

有一些对象其实我们只需要一个，比方说：线程池、缓存。这些类对象只能有一个实例。单例模式常常被用来管理共享资源，例如数据库连接或者线程池。

单例模式的实现见[单例模式 JAVA](https://liuyuqi21.github.io/2019/09/21/%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F-JAVA/)

# 命令模式
定义：命令模式将“请求”封装成对象，以便使用不同的请求、队列或者日志类参数化其他对象。命令模式也支持可撤销的操作。

# 适配器模式

# 外观模式

# 模板方法模式

# 迭代器模式

# 组合模式

# 状态模式

# 代理模式



# 推荐阅读
- [design-patterns-for-humans](https://github.com/kamranahmedse/design-patterns-for-humans#-simple-factory)
- [超全的设计模式简介（45 种）](https://learnku.com/articles/27556#b32af9)
- [用人话描述的设计模式--创建型设计模式](https://www.atjiang.com/design-patterns-for-humans-creational/)
- [用人话描述的设计模式--行为型设计模式](https://www.atjiang.com/design-patterns-for-humans-behavioral/)
- [用人话描述的设计模式--结构型设计模式](https://www.atjiang.com/design-patterns-for-humans-structual/)
- [设计模式（45种）](https://github.com/guanguans/notes/blob/master/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%EF%BC%8845%E7%A7%8D%EF%BC%89.md)