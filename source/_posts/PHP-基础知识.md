---
title: PHP 基础
date: 2019-04-10 20:36:32
tags: 
    - PHP
categories: PHP
---
## 函数

### 字符串函数
- strlen 获取字符串的长度
- strtoupper 将字符串中的小写字符转变为大写的字符， strtolower 将字符串中大写的字符转变为小写的字符
- ucfirst 首字符大写，ucwords 单词首字母大写
- trim 去空格 ltrim | rtrim 删除字符串 开头 | 结尾 的空白字符（或其他字符）
- strpos 查找字符串首次出现的位置，查找失败返回 false，应使用 === 来判断，
- strstr 查找字符串的首次出现，返回字符串(大小写敏感) stristr 查找字符串的首次出现，返回字符串(大小写不敏感)
- str_replace 替换字符串中的某个字符
- str_repeat 重复一个字符串
- str_pad 使用另一个字符串填充字符串为指定长度
- split 用正则表达式将字符串分割成数组
- implode 将数组组合成一个字符串，explode 使用一个字符串分割字符串成数组 join 是 implode 的别名
- strrev 字符串的反转，ps:汉字不能反转
- number_format 数字格式化
- wordwrap 打断字符串为指定数量的字串
- iconv 字符串按要求的字符编码来转换
- substr  字符串的截取函数，substr_compare 字符串的截取比较

### 时间日期函数
- date() 格式化时间戳
- strtotime() 将任何英文文本的日期时间描述解析为 Unix 时间戳
- mktime() 取得一个日期的 Unix 时间戳
- time() 返回当前的 Unix 时间戳
- microtime() 返回当前 Unix 时间戳和微秒数
- data_default_timezone_set() 设定用于一个脚本中所有日期时间函数的默认时区

### 打印处理函数
- print()
- printf()
- print_r()
- sprintf()
- var_dump()
- var_export()
- echo

### HTML函数
- htmlspecialchars() 将html标签转换为html实体
- htmlspecialchars_decode()将html实体转换为html标签
- strip_tags() 从字符串中去除 HTML 和 PHP 标记
- stripslashes()去除字符串中转义斜杠

### 数组处理函数
- array_search() 在数组中搜索给定的值 用来查找数组元素查找数据在数组中的位置，如果该数组有该数据，返回的是该数据在数组中的数组元素的下标或者键名；
- in_array() 判断某个数组元素值是否存在
- array_key_exists() 判断某个数组元素的标识符是否存在
- array_values() 从数组中取得所有的数组元素值，组成一个新的索引数组，如果正确，返回的是组成的索引数组，不正确则返回false
- array_keys() 从数组中取出所有的数组元素的标识符，组成一个新的索引数组
- extract() 将关联数组映射到一张变量表中，以键名当作变量名，以数组元素值当作变量值,如果对变量的值做了修改（重定义），那么他不会影响数组中的数组元素值
- list() 将数组中的元素值赋给不同的变量 结构：list(变量1,变量2,变量3...)=array(...);注意：只能对索引数组进行操作
- each() 返回数组当前元素的键值对，并将指针移动到下一个元素位置
- array_push(数组名，参数1，参数2，参数3...) 入栈：从堆栈结构的顶部压入数据的操作 array_pop(数组名) 出栈：从堆栈结构的顶部弹出（删除）数据的过程
- array_unshift(数组名，参数1，参数2，参数3...) 入队：从队列的头部压入一些数据，这种操作叫做入队 array_shift(数组名) 出队：从队列的头部删除一些数据，这种操作叫做出队
- array_merge($arr1,$arr2) 将参数列表中的数组进行合并
- array_slice(数组名，切割的起始位置，切掉的长度)切割数组中的连续的几个数组元素，切割后返回的是切割掉的数组元素组成的新数组，切割不成功返回空数组
- array_splice()去掉数组中的某一部分并用其它值取代
- sort() 本函数对数组进行排序。当本函数结束时数组单元将被从最低到最高重新安排 rsort()本函数对数组进行逆向排序（最高到最低）。
- ksort()对数组按照键名排序 krsort() 对数组按照键名逆向排序
- array_unique() 去除重复的数组元素值，返回去除重复元素值之后的数组
- array_count_values() 返回的数组，统计数组中所有的值出现的次数
- array_rand() 从数组中取出一个或多个随机的单元，并返回随机条目的一个或多个标识符
- array_sum() 将数组中的所有值的和以整数或浮点数的结果返回
- array_product() 计算乘积
- array_diff() 用来求数组的差集，用第一个数组减去第二个数组
- array_diff_key()
- arraydiffassoc()
- array_intersect()
- arrayintersectkey()

### 其他函数
- ip2long() 将 IPV4 的字符串互联网协议转换成长整型数字
- long2ip() 将长整型转化为字符串形式带点的互联网标准格式地址（IPV4）
- serilize 类的属性可以序列化后保存到 session 中，从而以后可以恢复整个类
- unserilize()
- isset()检测一个变量是否有设置 empty()检测一个变量是否为空
- is_bool() 检测变量是否为布尔类型
- is_float() 检测变量是否是浮点型
- is_int() 检测变量是否是整数
- is_string() 检测变量是否是字符串
- is_null() 检测变量是否是空类型
- ceil()进一 floor()舍去 round()四舍五入

### 防止SQL注入漏洞可以用哪些函数？
ddslashes()
mysql_escape_string()

### 超全局数组（预定义变量）
- `$_SESSION`
- `$_COOKIE`
- `$_SERVER`
    - 'GATEWAY_INTERFACE' 服务器使用的 CGI 规范的版本；例如，“CGI/1.1”。
    - 'SERVER_PORT' Web 服务器使用的端口。默认值为 “80”。如果使用 SSL 安全连接，则这个值为用户设置的 HTTP 端口。
    - 'SERVER_ADDR' 当前运行脚本所在的服务器的 IP 地址。
    - 'SERVER_NAME' 当前运行脚本所在的服务器的主机名。如果脚本运行于虚拟主机中，该名称是由那个虚拟主机所设置的值决定。
    - 'REQUEST_METHOD' 访问页面使用的请求方法；例如，“GET”, “HEAD”，“POST”，“PUT”。
    - 'REQUEST_TIME' 请求开始时的时间戳。从 PHP 5.1.0 起可用。
    - 'QUERY_STRING' query string（查询字符串），如果有的话，通过它进行页面访问。
    - 'DOCUMENT_ROOT' 当前运行脚本所在的文档根目录。在服务器配置文件中定义。
    - 'HTTP_HOST' 当前请求头中 Host: 项的内容，如果存在的话。
    - 'HTTP_REFERER' 引导用户代理到当前页的前一页的地址（如果存在）。由 user agent 设置决定。并不是所有的用户代理都会设置该项，有的还提供了修改 HTTP_REFERER 的功能。简言之，该值并不可信。
    - 'HTTP_USER_AGENT' 当前请求头中 User-Agent: 项的内容，如果存在的话。该字符串表明了访问该页面的用户代理的信息。一个典型的例子是：Mozilla/4.5 [en] (X11; U; Linux 2.2.9 i586)。除此之外，你可以通过 get_browser() 来使用该值，从而定制页面输出以便适应用户代理的性能。
    - 'REMOTE_ADDR' 浏览当前页面的用户的 IP 地址。
    - 'REMOTE_HOST' 浏览当前页面的用户的主机名。DNS 反向解析不依赖于用户的 REMOTE_ADDR。
    - 'REMOTE_PORT' 用户机器上连接到 Web 服务器所使用的端口号。
    - 'SCRIPT_FILENAME' 当前执行脚本的绝对路径。
    - 'SCRIPT_NAME' 包含当前脚本的路径。这在页面需要指向自己时非常有用。`__FILE__` 常量包含当前脚本(例如包含文件)的完整路径和文件名。
    - 'REQUEST_URI' URI 用来指定要访问的页面。例如 “/index.html”。
    - 'PATH_INFO' 包含由客户端提供的、跟在真实脚本名称之后并且在查询语句（query string）之前的路径信息，如果存在的话。例如，如果当前脚本是通过 URL `http://www.example.com/php/path_info.php/some/stuff?foo=bar` 被访问，那么 `$_SERVER['PATH_INFO']` 将包含 `/some/stuff`。
- `$_GET`
- `$_POST`
- `$_REQUEST` 默认情况下包含了 `$_GET`，`$_POST` 和 `$_COOKIE` 的数组。
- `$_FILES`
- `$_ENV`
- `$GLOBALS`

### 预定义常量
- `__LINE__`  文件中的当前行号。
- `__FILE__`  文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名。自 PHP 4.0.2 起，`__FILE__`  总是包含一个绝对路径（如果是符号连接，则是解析后的绝对路径），而在此之前的版本有时会包含一个相对路径。
- `__DIR__` 文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录。它等价于 dirname(`__FILE__`)。除非是根目录，否则目录中名不包括末尾的斜杠。（PHP 5.3.0中新增） =
- `__FUNCTION__`  函数名称（PHP 4.3.0 新加）。自 PHP 5 起本常量返回该函数被定义时的名字（区分大小写）。在 PHP 4 中该值总是小写字母的。
- `__CLASS__`   类的名称（PHP 4.3.0 新加）。自 PHP 5 起本常量返回该类被定义时的名字（区分大小写）。在 PHP 4 中该值总是小写字母的。类名包括其被声明的作用区域（例如 Foo\Bar）。注意自 PHP 5.4 起 `__CLASS__` 对 trait 也起作用。当用在 trait 方法中时，`__CLASS__` 是调用 trait 方法的类的名字。
- `__TRAIT__`  Trait 的名字（PHP 5.4.0 新加）。自 PHP 5.4 起此常量返回 trait 被定义时的名字（区分大小写）。Trait 名包括其被声明的作用区域（例如 Foo\Bar）。
- `__METHOD__`  类的方法名（PHP 5.0.0 新加）。返回该方法被定义时的名字（区分大小写）。
- `__NAMESPACE__`   当前命名空间的名称（区分大小写）。此常量是在编译时定义的（PHP 5.3.0 新增）。

### OOP
- static 静态，类名可以访问
- public 表示全局，类内部外部子类都可以访问
- private 表示私有的，只有本类内部可以使用
- protected 表示受保护的，只有本类或子类或父类中可以访问
- `__call()` 当调用不存在的方法时会自动调用的方法

#### 魔术方法
- `__autoload()` 在实例化一个尚未被定义的类是会自动调用次方法来加载类文件
- `__set()` 当给未定义的变量赋值时会自动调用的方法
- `__get()` 当获取未定义变量的值时会自动调用的方法
- `__construct()` 构造方法，实例化类时自动调用的方法
- `__destroy()` 销毁对象时自动调用的方法
- `__unset()` 当对一个未定义变量调用unset()时自动调用的方法
- `__isset()` 当对一个未定义变量调用isset()方法时自动调用的方法
- `__clone()` 克隆一个对象
- `__tostring()` 当输出一个对象时自动调用的方法

#### 面向对象6个基本原则
1. 单一职责原则
1. 开闭原则
1. 里氏替换原则
1. 依赖倒置原则
1. 接口隔离原则
1. 合成复用原则
1. 迪米特法则

### 题目
- 自己实现字符串反转

```php
function strrev($str){
    $len = strlen($str);
    $newstr = '';
    for ($i = $len; $i >= 0; $i--) {
        $newstr .= $str{$i};
    }
    return $newstr;
}
```
- 用PHP打印出前一天的时间，打印格式是2007年5月10日22:21:21 `echo date('Y-m-d H:i:s',strtotime('-1 day'));`
- 写出一个函数,尽可能的高效,从一个标准的URL里取出文件的扩展名例如:http://www.baidu.com/abc/de/fg.php/?id=1,需要取出 php 或者.php

```php
//方法一
$url = "http://www.baidu.com/abc/de/fg.php/?id=1";
//获得文件目录名字,http://www.baidu.com/abc/de/fg.php
echo $path = pathinfo($url, PATHINFO_DIRNAME);
//获得文件的扩展名,php
$path = pathinfo($path, PATHINFO_EXTENSION);
echo $path;

//方法二
$arr  =  parse_url('http://www.sina.com.cn/abc/de/fg.php?id=1');
result = pathinfo(result = pathinfo(arr['path']);
var_dump($arr);
var_dump($result['extension']);
```
- 写一个函数，可以遍历文件夹下的所有文件和文件夹。

```php
function get_dir_info($path){
    $handle = opendir($path);//打开目录返回句柄
    while (($content = readdir($handle)) !== false) {
        $new_dir = $path . DIRECTORY_SEPARATOR . $content;
        if ($content == '..' || $content == '.') {
            continue;
        }
        if (is_dir($new_dir)) {
            echo "<br>目录：" . $new_dir . '<br>';
            get_dir_info($new_dir);
        } else {
            echo "文件：" . $path . ':' . $content . '<br>';
        }
    }
}

et_dir_info($dir);
```
- 不用新变量直接交换现有两个变量的值

```php
list($a, $b) = array($b, $a);
```

#### 其他
- CGI 通用网关接口
- FastCGI 是一个协议，它是应用程序和WEB服务器连接的桥梁。Nginx并不能直接与PHP-FPM通信，而是将请求通过FastCGI交给PHP-FPM处理 用来提高cgi程序性能，启动一个master，在启动多个worker
- php-fpm是FastCGI的进程管理器，可以在启动的时候预先生成多个进程