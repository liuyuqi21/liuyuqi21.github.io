# 基础篇
了解大部分数组处理函数

字符串处理函数 区别 mb_ 系列函数

- 不同编码的个别语言所占字节数不同，mb_系列的函数在使用时可以多指定一个编码参数，方便处理不同编码的中文。
- strlen()会返回一个字符串所占字节数，而mb_strlen()会返回一个字符串的字符数。
- substr($str2, 2, 2)在$str为中文时可能会正好截取到一个汉字的一部分，这时就会发生乱码，而mb_substr($str, 2, 2, ‘utf-8’)指定编码后就不会发生乱码问题了，中文时即是取几个汉字。

& 引用，结合案例分析

== 与 === 区别

- ==是不带类型比较是否相同（比如100=='100'结果为true），===是带类型比较是否相同（100==='100'结果为false）

isset 与 empty 区别

全部魔术函数理解

static、$this、self 区别

private、protected、public、final 区别
OOP 思想
抽象类、接口 分别使用场景
Trait 是什么东西

- Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。使用时不是用 extend 继承，而是在类内部用 use 类名表示。
- 重名方法优先级问题：当前类的成员覆盖 trait 方法，而 trait 则覆盖被继承的方法。

echo、print、print_r 区别(区分出表达式与语句的区别)

- echo ，print 是语言结构。
- echo 没有返回值，print 返回值为1。
- var_dump() 和 print_r()是函数。

__construct 与 __destruct 区别

static 作用（区分类与函数内）手册 、SOF

- 声明类属性或方法为静态，就可以不实例化类而直接访问。静态属性不能通过一个类已实例化的对象来访问。
- 在函数中，static 变量是在函数退出时不会丢失其值的变量。

__toString() 作用
单引号'与双引号"区别
常见 HTTP 状态码，分别代表什么含义
# 进阶篇
Autoload、Composer 原理 PSR-4 、原理
Session 共享、存活时间
异常处理
如何 foreach 迭代对象
如何数组化操作对象 $obj[key];
如何函数化对象 $obj(123);
yield 是什么，说个使用场景 yield
PSR 是什么，PSR-1, 2, 4, 7

- PSR-1 基础编码规范
- PSR-2 编码风格规范
- PSR-4 自动加载规范
- PSR-7 HTTP 消息接口规范

如何获取客户端 IP 和服务端 IP 地址

- 客户端 IP：$_SERVER['REMOTE_ADDR']
- 服务端 IP：$_SERVER['SERVER_ADDR']

了解代理透传 实际IP 的概念


如何开启 PHP 异常提示

- php.ini 开启 display_errors 设置 error_reporting 等级
- 运行时，使用 ini_set(k, v); 动态设置

如何返回一个301重定向

- 设置 301 后脚本会继续执行，不要认为下面不会执行，必要时使用 die or exit。
    ```php
    http_response_code(301);
    header('Location: /option-a');
    exit;
    ```
如何获取扩展安装路径

- phpinfo()，页面查找 extension_dir
- 命令行 php -i|grep extension_dir
- 运行时 echo ini_get('extension_dir');

字符串、数字比较大小的原理，注意 0 开头的8进制、0x 开头16进制

- 字符串比较大小，从左(高位)至右，逐个字符 ASCII 比较
BOM 头是什么，怎么除去
- BOM 头是放在 UTF-8 编码的文件的头部的三个字符（0xEF,0xBB,0xBF）占用三个字节，用来标识该文件属于 UTF-8 编码。PHP 不能识别 BOM 头，所以 PSR-4 规定无 BOM 头的 UTF-8 格式。
- 检测
- 去除

什么是 MVC

依赖注入实现原理

如何异步执行命令

模板引擎是什么，解决什么问题、实现原理（Smarty、Twig、Blade）

如何实现链式操作 $obj->w()->m()->d();

Xhprof 、Xdebug 性能调试工具使用

索引数组 [1, 2] 与关联数组 ['k1'=>1, 'k2'=>2] 有什么区别

缓存的使用方式、场景

# 实践篇
给定二维数组，根据某个字段排序

如何判断上传文件类型，如：仅允许 jpg 上传

不使用临时变量交换两个变量的值 $a=1; $b=2; => $a=2; $b=1;

- list($b,$a)=array($a,$b);

strtoupper 在转换中文时存在乱码，你如何解决？php echo strtoupper('ab你好c')

- 使用 mb_ 系列函数

Websocket、Long-Polling、Server-Sent Events(SSE) 区别
"Headers already sent" 错误是什么意思，如何避免

# 算法篇
快速排序（手写）
冒泡排序（手写）
二分查找（了解）
查找算法 KMP（了解）
深度、广度优先搜索（了解）
LRU 缓存淘汰算法（了解，Memcached 采用该算法）
# 数据结构篇（了解）
堆、栈特性
队列
哈希表
链表
# 对比篇
Cookie 与 Session 区别
GET 与 POST 区别
include 与 require 区别
include_once 与 require_once 区别
Memcached 与 Redis 区别
MySQL 各个存储引擎、及区别（一定会问 MyISAM 与 Innodb 区别）
HTTP 与 HTTPS 区别
Apache 与 Nginx 区别
define() 与 const 区别
traits 与 interfaces 区别 及 traits 解决了什么痛点？
Git 与 SVN 区别
# 数据库篇
MySQL
CRUD
JOIN、LEFT JOIN 、RIGHT JOIN、INNER JOIN
UNION
GROUP BY + COUNT + WHERE 组合案例
常用 MySQL 函数，如：now()、md5()、concat()、uuid()等
1:1、1:n、n:n 各自适用场景
了解触发器是什么，说个使用场景
数据库优化手段
索引、联合索引（命中条件）
分库分表（水平分表、垂直分表）
分区
会使用 explain 分析 SQL 性能问题，了解各参数含义
重点理解 type、rows、key
Slow Log（有什么用，什么时候需要）
MSSQL(了解)
查询最新5条数据
NOSQL
Redis、Memcached、MongoDB
对比、适用场景（可从以下维度进行对比）
持久化
支持多钟数据类型
可利用 CPU 多核心
内存淘汰机制
集群 Cluster
支持 SQL
性能对比
支持事务
应用场景
你之前为了解决什么问题使用的什么，为什么选它？
# 服务器篇
查看 CPU、内存、时间、系统版本等信息
find 、grep 查找文件
awk 处理文本
查看命令所在目录
自己编译过 PHP 吗？如何打开 readline 功能
如何查看 PHP 进程的内存、CPU 占用
如何给 PHP 增加一个扩展
修改 PHP Session 存储位置、修改 INI 配置参数
负载均衡有哪几种，挑一种你熟悉的说明其原理
数据库主从复制 M-S 是怎么同步的？是推还是拉？会不会不同步？怎么办
如何保障数据的可用性，即使被删库了也能恢复到分钟级别。你会怎么做。
数据库连接过多，超过最大值，如何优化架构。从哪些方便处理？
502 大概什么什么原因？ 如何排查 504呢？
# 架构篇
偏运维（了解）：
负载均衡（Nginx、HAProxy、DNS）
主从复制（MySQL、Redis）
数据冗余、备份（MySQL增量、全量 原理）
监控检查（分存活、服务可用两个维度）
MySQL、Redis、Memcached Proxy 、Cluster 目的、原理
分片
高可用集群
RAID
源代码编译、内存调优
缓存
工作中遇到哪里需要缓存，分别简述为什么
搜索解决方案
性能调优
各维度监控方案
日志收集集中处理方案
国际化
数据库设计
静态化方案
画出常见 PHP 应用架构图
框架篇
ThinkPHP（TP）、CodeIgniter（CI）、Zend（非 OOP 系列）
Yaf、Phalcon（C 扩展系）
Yii、Laravel、Symfony（纯 OOP 系列）
Swoole、Workerman （网络编程框架）
对比框架区别几个方向点
是否纯 OOP
类库加载方式（自己写 autoload 对比 composer 标准）
易用性方向（CI 基础框架，Laravel 这种就是高开发效率框架以及基础组件多少）
黑盒（相比 C 扩展系）
运行速度（如：Laravel 加载一大堆东西）
内存占用
# 设计模式
单例模式（重点）
- 可以避免大量的 new 操作消耗的资源
    1. 构造函数需要标记为 private
    1. 保存了实力的静态成员变量
    1. 获取实例的公共静态方法
```php
class Singleton{
    private static $instance;
    public static function getInstance(){
        if(self::$instance==null){
            self::$instance=new self();
        }
        return self::$instance;
    }
    private function __construct(){}
    private function __clone(){}
}
```
工厂模式（重点）

- 简单工厂：
    - 接口中定义一些方法
    - 实现接口的类实现这些方法
    - 工厂类用以实例化对象
    - 优点：为系统结构提供了灵活的动态扩展机制。方便维护
    ```php
    class productA
    {
        function __construct(){
            echo 'productA';
        }
    }
    class productB
    {
        function __construct(){
            echo 'productB';
        }
    }
    class Factory
    {
        public static function CreateProduct($name){
            if($name=='A'){
                return new productA();
            }else if($name=='B'){
                return new productB();
            }
        }
        $A=Factory::CreateProduct('A');
        $B=Factory::CreateProduct('B');
    }
    ```
- 工厂模式：工厂模式的核心是工厂类不再负责所有对象的创建，而是将具体的创建工作交给子类，成为一个抽象工厂的角色，他仅负责给出具体工厂必须实现的接口，而不接触哪一个产品类应当被实例化这种细节
    ```php
    interface Procduct{
        public function getProduct();
    }
    class ProductA extends Product{
        public function getProduct(){
            echo 'A';
        }
    }
    class ProductB extends Product{
        public function getProduct(){
            echo 'B';
        }
    }
    abstract class Factory{
        abstract static function createProduct();
    }
    class ProductAFactory extends Factory{
        public static function createProduct(){
            return new ProductA();
        }
    }
    class ProductBFactory extends Factory{
        public static function createProduct(){
            return new ProductB();
        }
    }
    $productA=ProductAFactory::createProduct();
    $productA->getProduct();
    $productB=ProductBFactory::createProduct();
    $productB->getProduct();
    ```

观察者模式（重点）

```php
//事件生产者
abstruct class EventGenerator(){
    private $observers=[];
    function addObserver(Observer $observer){
        $this->observers[]=$observer;
    }
    function notify(){
        foreach($this->$observers as $item){
            $item->update();
        }
    }
    function delObserber($observer){
        $key=array_search($observer,$this->observers);
        array_splice($this->observers,$key,1);
    }
}
//观察者接口
interface Observer{
    function update($event_info=null);
}
//实现观察者逻辑接口
class Observer1 implements Observer{
    function update($event_info=null){
        echo '给用户发邮件';
    }
}
class Observer2 implements Observer{
    function update($event_info=null){
        echo '给商户发邮件';
    }
}
//在主方法中调用
class Order extends EventGenerator{
    function trigger(){
        echo '下单成功';
        $this->notify();
    }
}
$event=new Order();
$event->addOvserver(new Observer1());
$event->addOvserver(new Observer2());
$event->trigger();
```

依赖注入（重点）

- 减少类和类之间联系
- 容器：
    - 降低耦合度
    - 实现惰性加载
    - 便于管理
```php
class LunTai {
   function roll(){
       echo 'roll';
   }
}
class Car{
    protected $luntai;
    function __construct($luntai){
        $this->luntai=$luntai;
    }
    function run(){
        $this->$luntai->roll();
        echo 'run';
    }
}
$luntai=new LunTai();
$car=new Car($luntai);
$car->run();

//容器
class Container{
    static $register=[];
    static function bind($name,Closure $col){
        self::$register[$name]=$col;
    }
    static function make($name){
        $col=self::$register[$name];
        return $col();
    }
}
Container::bind('luntai',function(){
    return new LunTai()
});
Container::bind('car',function(){
    return new Car(Container::make('luntai'));
});
$car=Container::make('car');
$car->run();

// 创建一个容器（后面称作超级工厂）
$container = new Container;

// 向该 超级工厂 添加 超人 的生产脚本
$container->bind('superman', function($container, $moduleName) {
    return new Superman($container->make($moduleName));
});

// 向该 超级工厂 添加 超能力模组 的生产脚本
$container->bind('xpower', function($container) {
    return new XPower;
});

// 同上
$container->bind('ultrabomb', function($container) {
    return new UltraBomb;
});

// ******************  华丽丽的分割线  **********************
// 开始启动生产
$superman_1 = $container->make('superman', ['xpower']);
$superman_2 = $container->make('superman', ['ultrabomb']);
$superman_3 = $container->make('superman', ['xpower']);
```

装饰器模式
代理模式
组合模式
# 安全篇
SQL 注入
XSS 与 CSRF
输入过滤
Cookie 安全
禁用 mysql_ 系函数
数据库存储用户密码时，应该是怎么做才安全
验证码 Session 问题
安全的 Session ID （让即使拦截后，也无法模拟使用）
目录权限安全
包含本地与远程文件
文件上传 PHP 脚本
eval 函数执行脚本
disable_functions 关闭高危函数
FPM 独立用户与组，给每个目录特定权限
了解 Hash 与 Encrypt 区别
# 高阶篇
PHP 数组底层实现 （HashTable + Linked list）
Copy on write 原理，何时 GC
PHP 进程模型，进程通讯方式，进程线程区别
yield 核心原理是什么
PDO prepare 原理
PHP 7 与 PHP 5 有什么区别
Swoole 适用场景，协程实现方式
# 前端篇
原生获取 DOM 节点，属性
盒子模型
CSS 文件、style 标签、行内 style 属性优先级
HTML 与 JS 运行顺序（页面 JS 从上到下）
JS 数组操作
类型判断
this 作用域
.map() 与 this 具体使用场景分析
Cookie 读写
JQuery 操作
Ajax 请求（同步、异步区别）随机数禁止缓存
Bootstrap 有什么好处
跨域请求 N 种解决方案
新技术（了解）
ES6
模块化
打包
构建工具
vue、react、webpack、
前端 mvc
优化
浏览器单域名并发数限制
静态资源缓存 304 （If-Modified-Since 以及 Etag 原理）
多个小图标合并使用 position 定位技术 减少请求
静态资源合为单次请求 并压缩
CDN
静态资源延迟加载技术、预加载技术
keep-alive
CSS 在头部，JS 在尾部的优化（原理）
# 网络篇
IP 地址转 INT
192.168.0.1/16 是什么意思
DNS 主要作用是什么？
IPv4 与 v6 区别
网络编程篇
TCP 三次握手流程
TCP、UDP 区别，分别适用场景
有什么办法能保证 UDP 高可用性(了解)
TCP 粘包如何解决？
为什么需要心跳？
什么是长连接？
HTTPS 是怎么保证安全的？
流与数据报的区别
进程间通信几种方式，最快的是哪种？
fork() 会发生什么？
# API 篇
RESTful 是什么
如何在不支持 DELETE 请求的浏览器上兼容 DELETE 请求
常见 API 的 APP_ID APP_SECRET 主要作用是什么？阐述下流程
API 请求如何保证数据不被篡改？
JSON 和 JSONP 的区别
数据加密和验签的区别
RSA 是什么
API 版本兼容怎么处理
限流（木桶、令牌桶）
OAuth 2 主要用在哪些场景下
JWT
PHP 中 json_encode(['key'=>123]); 与 return json_encode([]); 区别，会产生什么问题？如何解决
# 加分项
了解常用语言特性，及不同场景适用性。
PHP VS Golang
PHP VS Python
PHP VS JAVA
了解 PHP 扩展开发
熟练掌握 C