---
title: PHP 面试知识点
date: 2019-04-16 21:53:20
tags:
---

# 引用变量

## 概念
在 php 中意味着用不同的名字访问同一个变量内容

## 定义方式
使用 & 符号 

## 工作原理
COW（Copy On Write）机制：只有在 $a 或者 $b 进行修改操作，才会新开辟一块空间
```php
    $a = range(0,1000);
    var_dump(memory_get_usage());//查看内存使用情况
    //定义变量b，将a的值赋值给b
    //COW机制 Copy On Write 只有在$a或者$b进行修改操作，才会新开辟一块空间
    $b = $a;//此时a,b指向同一块内存空间
    $b = &$a;//此时修改$a也不会开辟新的空间。a，b始终指向同一块内存
    var_dump(memory_get_usage());
    $a = range(0,1000);//a指向新开辟了空间
    var_dump(menory_get_usage());
```

## zval 变量容器 
`xdebug_debug_zval('a');` 打印出变量a的结构
- refcount = 1 代表有一个对象指向这个内存空间
- is_ref = 0 表示是不是引用，0 代表 false

## unset 只会取消引用，不会销毁空间
```php
    $a=1;
    $b=&$a;
    unset($b);//取消b的引用
    echo $a;
```

## 对象本身就是引用传递,不会进行空间复制，如果要复制，用 clone()
 ```php
    class Person
    {
        public $name='a';
    }
    $p1=new Person;
    xdebug_debug_zval('p1');
    $p2=$p1;
    xdebug_debug_zval('p1');//refcount=2,if_ref=0
    $p2->name='b';
    xdebug_debug_zval('p1');//p1的值已经被改变为b，refcount=2
```

## 写出如下程序运行的结果 bcc
```php
    $data=['a','b','c'];
    foreach($data as $key => $val){
        $val=&$data[$key];
    }
```

# 常量及数据类型

## php的字符串的定义方式及各自区别
- 定义方式：单引号，双引号，heredoc 和 newdoc
- 单引号不能解析变量，不能解析转义字符，效率更高
- 双引号可以解析变量，变量字符用{}包含，可以解析转义字符，可以使用.连接
- heredoc 类似双引号，newdoc 类似于单引号，用来处理大文本

## 数据类型
- 三大数据类型（标量，复合，特殊）
- 浮点类型不能运用于比较运算
- false的七种情况：0,0.0,' ','0',false,array(),NULL,
- 超全局数组
- NULL的三种情况：直接赋值null，未定义的变量，unset销毁的变量
- 常量：定义方式
    - const 更快，是语言函数，可以定义类常量
    - define 是函数，不能定义类常量

## 运算符
- @ 错误控制符，当将其放置在一个 php 表达式之前，该表达式可能将任何错误信息都忽略掉
- 运算符的优先级 由高到低 递增递减 算数运算符 大小比较 逻辑与 逻辑或 三目 赋值 and xor or
- 比较运算符 == 和 === 
- 等值判断（false 的七种情况都是相等的）
- 递增递减运算符不影响 **布尔值**
- **null** 递增为1

# 流程控制
- foreach() 开始遍历时会对数组进行 reset() 操作，但是遍历结束后指针指向末尾
- while(),list(),each()不会 reset()
- switch 后面的控制表达式的数据类型只能是整性，浮点型，字符串

# 自定义函数及内部函数
- 变量的作用域即它定义的上下文背景，包含了 include 和 require 引入的文件
- 函数体内部的变量是内部变量，函数体内可以用 global 关键字来使用外部变量
```php
    $outer = "str";
    function fun(){
        global $outer;//使用外部变量
        $GLOBALS['outer'];也可以用这种方式
    echo $outer;
    }
```
- 默认情况下函数通过值传递，如果允许函数修改它的值，必须通过引用传递
- include/require 语句包含文件，如果没有给出路径名，就会从 include_path 来查找，如果没找到，就从调用脚本文件所在的目录和当前工作目录下寻找
- 加载过程中未找到文件 include 会发出一条警告，require 会发出致命错误

## static关键字
1. 仅初始化一次
1. 初始化时需要赋值
1. 每次执行函数该值会保留
1. static 修饰的变量是局部的，仅在函数内部有效
1. 可以记录函数的调用次数，从而在某些条件下终止递归

## 后期静态绑定
使用 self 对当前类的静态引用，取决于定义当前方法所在的类
```php
<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        self::who();
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test();
//输出 A
``` 
后期静态绑定本想通过引入一个新的关键字表示运行时最初调用的类来绕过限制。简单地说，这个关键字能够让你在上述例子中调用 test() 时引用的类是 B 而不是 A。最终决定不引入新的关键字，而是使用已经预留的 static 关键字。
```php
<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        static::who(); // 后期静态绑定从这里开始
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test();
//输出 B
```

# 文件及目录处理
- fopen()函数 用来打开一个文件,打开时需要指定打开模式
- 写入函数 fwrite() fputs()
- 读取函数 fread() fgets() fgetc()
- 关闭函数 fclose() 
- 遍历目录
```php
    function loopDir($dir){
    $handle=opendit($dir);
    while(false!==($file=readdir($handle))){
        if($file!='.'&&$file!='..'){
            echo $file;
            if(filetype($dir.'/'.$file)=='dir'){
                loopDir(filetype($dir.'/'.$file);
                }
            }
        }
    }
```

# php 运行原理
- CGI：解决 php 解释器和服务器之间通信
- FastCGI：是一种协议每次处理完进程不会 kill 掉，而是保留，使这个进程一次处理多个请求
- PHP-FPM：提供了FastCGI的实现，是 FastCGI 的进程管理器，有 master 进程和 worker 进程，master 进程只有一个，负责监听端口，接收来自服务器的请求，worker 进程有多个，可以在 php-fpm 配置，每个进程内部嵌入 php 解析器，worker 负责处理代码，master 监听端口。

# 会话控制技术

## Cookie
- setcookie($name,$value,$exppire,$path,$domain,$secure)
- $_COOKIE 只读 无法unset
- 删除 cookie setcookie($name,'',time()-1000)

## Session
- session_start()
- $_SESSION
- 删除session $_SESSION=[]
- 销毁session  session_destory()
- 配置 
```
    //垃圾回收
    session.gc_probability=1
    session.gc_divisor=100
    session.gc_maxlifetime=1440

    session.save_handler//可以配置存储句柄 redis memcached
```
- 如果cookie被禁用 ```<a href="1.php?<?php echo SID;?>">下个页面</a>```

# 框架原理
- Model View Controller
- 单一入口的工作原理：用一个程序文件处理所有的 HTTP 请求，根据请求时的参数的不同区分不同模块和操作的请求
index.php?r-user/reg => new user(); 调用 reg方法 
    - 优势：可以进行统一的安全性检查，集中处理程序
    - 劣势：URL 不美观（URL 重写），处理效率稍低
- 模板引擎的理解：PHP 是一种 HTML 内嵌式的在服务端执行的脚本语言，但是 PHP 有很多使 PHP 代码和 HTML 代码分开的模板引擎
- 模板引擎的工作原理就是一个庞大的正则表达式库
- php 框架的差异和优缺点：Laravel 和 ThinkPHP
- 框架特性

## php7 新特性
- ?? NULL 合并运算符
- <=> 组合比较符
- 函数返回值类型声明
- 标量类型声明
- try...catch 增加多条件判断，更多 Error 错误可以进行异常处理
- 增加了匿名类，现在支持通过 new class 来实例化一个匿名类，这可以用来替代一些 “用后即焚” 的完整类定义类
- define 定义常量数组
- Unicode codepoint 转译语法
- 为 unserialize() 提供过滤
- Throwable 接口
- use 批量声明

### 为什么 PHP7 比 PHP5 性能提升了
- 变量存储字节减小，减少内存占用，提升变量操作速度
- 改善数据结构，数组元素和 hash 映射表被分配在同一块内存里，降低了内存占用、提升了 cpu 缓存命中率
- 改进了函数的调用机制，通过优化参数传递的环节，减少了一些指令，提高执行效率

### 异常处理
- PHP7中更多的Error变为可捕获的Exception返回给开发者，如果不进行捕获则为Error，如果捕获就变为一个可在程序内处理的Exception。默认情况下，Error会直接导致程序中断，而PHP7则通过try / catch程序块捕获并且处理，让程序继续执行下去，为程序员提供更灵活的选择。