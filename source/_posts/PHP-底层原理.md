---
title: PHP 底层原理
date: 2019-04-16 21:52:55
tags: 
    - PHP
categories: PHP
---

## 生命周期和 Zend 引擎
SAPI 指的是 PHP 集体应用的编程接口，脚本执行的开始都是以 SAPI 接口实现开始的

## SAPI 概述

## PHP 脚本的执行
- php 虽然是一个脚本语言。但不是靠解释器解释，而是 zend 虚拟机，屏蔽了操作系统的区别。
- php 代码编译成 opcode，用 zend 虚拟机来执行 opcode，php 脚本一结束，opcode 就清除了
- php 本身不支持缓存，但是 apc，xcache 等加速器，实现了这样的效果

## 执行过程
Opcode 是一种 php 脚本编译后的中间语言

- Scanning（Lexing），将 php 代码转换为语言片段（Tokens）
- parsing，将 Tokens 转换成简单而有意义的表达式
- Compilation，将表达式编译成 Opcodes
- Execution，顺次执行 Opcodes，每次一条，从而实现 PHP 脚本的功能

## 变量的底层实现
```c
struct _zval_struct{
    union {
        long lval;
        double dval;
        struct{
            char *val;
            int len;//字符串长度
        }str;
        HashTable *ht;
        zend_object_value obj;
        zend_ast *ast;
    }value;
    //变量的值，是个联合体
    zend_uint refcount_gc //指向次数
    zend_uchar type //变量类型
    zend_uchar is_ref_gc //是否引用
}

```
- 在php中，字符串长度直接体现在其结构体中。strlen速度非常快

## PHP 中有 8 种数据类型，为什么联合体中只有 5 种
1. NULL，直接 zval->type=IS_NULL 就可以表示，不必设置 value 的值。
2. BOOL，zval->type=IS_BOOL,再设置 lval=1/0。
3. Resource，资源型，往往是服务器上打开的一个接口，lval=服务器上打开的接口的编号。

## 符号表
符号表是一张哈希表 里面存储了变量名 -> 变量的 zval 结构体的地址。

在传值赋值时，以 ` $a=3;$b=$a;`  为例，并没有再次产生结构体，而是两个变量共用一个结构体，refcount_gc 值为 2。

a，b 指向同一结构体，修改其中一方时，结构体会分裂成两个，这种特点称为 cow（copy on write）。

引用时，if_ref_gc=1,说明这个变量是引用，共用一个结构体，修改时直接改这个结构体的值，不分裂。

如果 `$a=3;$b=$a; $c=&$a;` 会强制分裂，b分裂一份结构体。

数组的 zvalue 里面存放的是指针，指向哈希表，哈希表里面就是数组的键和存放数组值的 zval
```php
$arr=[0,1,2,3];
$tmp=$arr;
$arr[1]=11;//$tmp[1]=1
```
此时发生了分裂，数组的一号单元产生了一个新的结构体，但是0，2，3对应的还是同一个结构体
```php
$arr=[0,1,2,3];
$x=&$arr[1];
$tmp=$arr;
$arr[1]=11;//$tmp[1]=11
```
此时强制分裂失效

## 弱类型的实现

## foreach
- foreach 循环后，数组指针停留在数组结尾
- foreach 操作的是 arrcopy，是 arr 的复制

## 符号表与作用域
- 当执行函数时，会生成函数的“执行环境结构体”，包含函数名，参数，执行步骤，所在的类，以及为这个函数生成一个符号表，符号表统一放在栈上，并把active_symbol_table指向刚产生的符号表，这个结构体是_zend_execute_data{}结构体，这个结构体中，有两个重要信息
- *op_array 是函数的执行步骤
- *hash_table-->symbol_table 这个函数对应的符号表
- 静态变量 放在op_array里，函数无论调用多少次。op_array只有一个，所以操作的是共同的变量
## 常量
- define，值为对象时，会把对象转成标量来存储，需要__toString魔术方法
- 常量的符号表只有一个，所以全局有效，变量，每调用一次，生成一个独立的符号表
## 对象
- 对象存储在 handle 里，handle 指向 hash 表中的对象
## 垃圾回收
PHP 5.3 版本之前都是采用引用计数的方式管理内存，PHP 所有的变量存在一个叫 zval 的变量容器中，当变量被引用的时候，引用计数会 + 1，变量引用计数变为 0 时，PHP 将在内存中销毁这个变量。

但是引用计数中的循环引用，引用计数不会消减为 0，就会导致内存泄露。
而php中内存对象就是zval，而计数器就是refcount__gc。
- 循环引用问题：
```php
$a=array("one");
$a[]=&$a;
unset($a);
```

在 5.3 版本之后，做了这些优化：
1. 并不是每次引用计数减少时都进入回收周期，只有根缓冲区满额后在开始垃圾回收；
1. 可以解决循环引用问题；
1. 可以总将内存泄露保持在一个阈值以下。

有几个基本准则：
1. 如果一个 zval 的 refcount 增加，那么此 zval 还在使用，不属于垃圾
1. 如果一个 zval 的 refcount 减少到 0，那么 zval 可以被释放掉，不属于垃圾
1. 如果一个 zval 的 refcount 减少之后大于 0，那么此 zval 还不能被释放，此 zval 可能成为一个垃圾。

新的GC算法目的就是防止循环引用的变量引起内存泄露问题。在PHP中GC算法，当节点缓冲区满了之后，垃圾分析算法就会启动，并且会释放掉发现的垃圾，从而回收内存。
首先PHP会分配一个固定大小的“根缓冲区”，这个缓冲区用于存放固定数量的zval，这个数量默认是10,000，如果需要修改则需要修改源代码Zend/zend_gc.c中的常量GC_ROOT_BUFFER_MAX_ENTRIES然后重新编译。
由上文我们可以知道，一个zval如果有引用，要么被全局符号表中的符号引用，要么被其它表示复杂类型的zval中的符号引用。因此在zval中存在一些可能根（root）。这里我们暂且不讨论PHP是如何发现这些可能根的，这是个很复杂的问题，总之PHP有办法发现这些可能根zval并将它们投入根缓冲区。
当根缓冲区满额时，PHP就会执行垃圾回收，此回收算法如下：
1. 对每个根缓冲区中的根zval按照深度优先遍历算法遍历所有能遍历到的zval，并将每个zval的refcount减1，同时为了避免对同一zval多次减1（因为可能不同的根能遍历到同一个zval），每次对某个zval减1后就对其标记为“已减”。
2. 再次对每个缓冲区中的根zval深度优先遍历，如果某个zval的refcount不为0，则对其加1，否则保持其为0。
3. 清空根缓冲区中的所有根（注意是把这些zval从缓冲区中清除而不是销毁它们），然后销毁所有refcount为0的zval，并收回其内存。

## 相关文章
- [TIPI 深入理解 PHP 内核](http://www.php-internals.com/book/)
- [深入理解PHP7内核之zval](http://www.laruence.com/2018/04/08/3170.html)
- [内存管理与垃圾回收](https://segmentfault.com/a/1190000013893628)(https://www.jianshu.com/p/63a381a7f70c)