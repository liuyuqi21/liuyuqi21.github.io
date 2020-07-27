## 路由
- 基本路由
```php
Route::get('/test', function () {
    return '基本路由';
});
```
- 带必选参数的路由
```php
//路由里的参数不需要和闭包里的参数同名
Route::get('/test1/{id}', function ($id) {
    return "必选参数的路由$id";
});
```
- 带可选参数的路由
```php
//给参数设置默认值
Route::get('/test1/{id?}', function ($id = 0) {
    return "可选参数的路由$id";
});
```
- 参数的正则约束
```php
//正则约束的是路由参数，跟路由参数保持一致
Route::get('/testrule/{$id}', function ($id) {
    return '参数的正则约束';
})->where('id', '\d+');

//多参数的正则约束
Route::get('/testrule/{name}/{age}', function ($n, $a) {

})->where(['name' => 'w+', 'age' => '\d+']);
```
## 控制器
- 
```php
public function show(User $user)
    {
        return view('users.show', compact('user'));
    }
```
Laravel 会自动解析定义在控制器方法（变量名匹配路由片段）中的 Eloquent 模型类型声明。在上面代码中，由于 show() 方法传参时声明了类型 —— Eloquent 模型 User，对应的变量名 $user 会匹配路由片段中的 {user}，这样，Laravel 会自动注入与请求 URI 中传入的 ID 对应的用户模型实例。
此功能称为 『隐性路由模型绑定』，是『约定优于配置』设计范式的体现，同时满足以下两种情况，此功能即会自动启用：
1. 路由声明时必须使用Eloquent模型的单数小写格式来作为路由片段参数
1. 控制器方法传参中必须包含对应的Eloquent模型类型提示，并且是有序的


## 响应
```php
//基本响应
response('hello world',200)->header('content-type','text/html;charset=utf8');
//设置Cookie
response('')->withCookie('name','value',10);
dd(Cookie::get('name'));//Cookie门面
//ajax
response()->json(['name'=>'aaa']);
```
## 数据库
### 原生sql语句
- 插入返回true或者false
- 查询返回一个数组，其中每个元素是一个对象
- 更新操作返回受影响行数，判断失败要用===false判断
- 删除操作返回受影响行数
### 查询构造器
- DB::table('user')//用不带表前缀的表名
- insert返回true或者false，insertGetId返回自增主键
### 设置查询监听
- 在Providers\AppServiceProviders.php的boot函数中添加
    ```php
    public function boot()
    {
        \DB::listen(function($query){
            var_dump($query->sql);
            var_dump($query->bindings);
            echo '<br>';
        })
    }
    ```
## 监听事件/观察者模式
使用 ```php artisan make:event``` 事件名称，在 app/Evnent 中就会自动生成一个文件，一个事件可以被一个或多个监听器监听，也就是观察者模式，在 EventServiceProvider 中的 $listen 中可以定义时间和监听器 
```php
 protected $listen = [
    'App\Events\UserLogin' => [
        'App\Listeners\DoSomething1',
        'App\Listeners\Dosomething2',
    ],
];
```
执行 ```php artisan event:generate```,就可以在 app/Listener 目录生成监听器。
## 安装组件
- 下载
- 注册到laravel中，先修改config/app.php文件中的providers，然后注册别名，修改aliases
## 用户认证
- 在模型里面要实现接口，App\User已经实现了所以直接继承
- trait是实现接口的方法，在类里面可以直接use
## 文件上传
- 文件存储在storage/public里面，无法访问，所以要添加软链接 php artisan storage:link
## 软删除 
- 在模型里面引入SoftDeletes
- 包含trait use SoftDeletes;
- 定义属性代表软删除字段 $dates=['deleted_at'];

# 注意
- $user->topics 返回的是数据集合， $user->topics()返回的是数据库查询语句。$user->topics == $user->topics()->get()。
- 使用hash_equals()比较两个字符串，无论它们是否相等，本函数的时间消耗是恒定的。本函数可以用在需要防止时序攻击的字符串比较场景中， 例如，可以用在比较 crypt() 密码哈希值的场景。
- N+1问题
- 方法 with() 提前加载了我们后面需要用到的关联属性 user 和 category，并做了缓存。后面即使是在遍历数据时使用到这两个关联属性，数据已经被预加载并缓存，因此不会再产生多余的 SQL 查询
- XSS 也称跨站脚本攻击 (Cross Site Scripting)，恶意攻击者往 Web 页面里插入恶意 JavaScript 代码，当用户浏览该页之时，嵌入其中 Web 里面的 JavaScript 代码会被执行，从而达到恶意攻击用户的目的。有两种方法可以避免 XSS 攻击：

第一种，对用户提交的数据进行过滤；
第二种，Web 网页显示时对数据进行特殊处理，一般使用 htmlspecialchars() 输出

# Laravel 源码

## 生命周期
1. 请求 index.php
1. 注册 composer 自动加载
1. 创建服务容器实例
1. 通过服务容器去解析内核实例，将 HTTP 内核定义的中间件组注册到路由器
1. 将 HTTP 请求实例注册到服务容器，并且通过启动引导程序来加载环境变量、全局配置、异常处理、注册门面、注册服务提供者、启动服务等，随后请求被分发到匹配的路由，在路由中执行中间件以过滤不满足校验规则的请求，只有通过中间件处理的请求才最终处理实际的控制器或匿名函数生成响应结果
1. 获取到响应结果后发送给请求的客户端
1. 运行程序 $kernel->terminate() 做一些善后清理工作，并最终退出脚本

## 依赖注入，控制反转
依赖注入模式：不需要自己修改内容，改成由外部传递。
laravel 中的依赖注入用反射机制实现

## 非入侵性 No instrusive
- 框架的目标之一是非侵入性（No intrusive）
- 组件可以直接拿到另一个应用或框架之中使用
- 增加组件的可重用性（Reusability）

## 容器 Container
- 管理对象的生成，资源取得，销毁等生命周期
- 件里对象与对象之间的依赖关系
- 启动容器后，所有对象直接取用，不用编写一行代码来产生对象，或是建立对象之间的依赖关系

## IoC 控制反转 Inversion of Control
- 依赖关系的转移
- 依赖抽象而非实践

## DI 依赖注入 Dependency Injection
- 不必自己在代码中维护对象的依赖
- 容器自动根据配置，将依赖注入指定对象
### 依赖注入的三种方式
- Setter injection 使用setter方法
- Constructor injection 使用构造函数
- Property Injection 直接设置属性

## AOP 面向切片编程 Aspect-oriented programming
- 无需修改任何一行程序代码，将功能加入至原先的应用程序中，也可以在不修改任何程序的情况下移除。


# 参考文章
- [PHP程序员如何理解IoC/DI](https://segmentfault.com/a/1190000002411255)
- [laravel 学习笔记 —— 神奇的服务容器](https://www.insp.top/article/learn-laravel-container)
- [浅谈 Laravel 设计模式](https://learnku.com/laravel/t/1954/on-laravel-design-pattern#3e05a8)
- [laravel核心知识学习](https://github.com/cxp1539/laravel-core-learn)
- [Laravel 中管道设计模式的使用 —— 中间件实现原理探究](https://laravelacademy.org/post/3088.html?utm_source=tuicool&utm_medium=referral)
- [laravel 管道设计模式 —— 代码理解](https://www.cnblogs.com/jade640/p/6773125.html)
- [Laravel 源码分析——看一次 Http 请求到响应](https://juejin.im/entry/591ab0a8128fe1005cded993)