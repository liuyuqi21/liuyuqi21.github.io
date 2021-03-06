---
title: MySQL 查询优化
date: 2019-05-11 21:45:54
tags: 
    - MySQL
category: 基础 
---
# 分析
先说明如何定位低效 SQL 语句，然后根据 SQL 语句可能低效的原因做排查，先从索引着手，如果索引没有问题，考虑其他几个方面，数据访问的问题，长难语句的问题还是一些特定类型优化的问题
1. 通过脚本，刷新观察 status
1. 是否周期性故障或者波动
1. 一般由访问高峰或缓存崩溃引起，加缓存并更改缓存失效策略，使失效时间分散或夜间定时失效
1. show processlist 或开启慢查询 获取有问题的 sql
1. profiling 分析语句及 explain 分析语句
    1. 如果语句等待时间长：调优服务器参数（如缓冲区，线程数）
    1. 如果语句执行时间长：可能是表设计有缺陷，索引没优化 语句没优化

## 排查命令
- `show status` 返回计数器
- `show global status` 查看服务器级别的所有计数
- `show processlist` 观察是否有大量线程处于不正常的状态或特征

## 慢查询日志
- 开启慢查询日志
    1. 以命令行的方式设置参数，不需要重启 MySQL 服务 `set global slow_query_log = ON; `
    1. 以配置文件的方式设置，需要重启 MySQL 服务。
- 设置 long_query_time 值：`set long_query_time=10;`
- 查看慢查询日志：`more show-query.log` 或者 `mysqldumpslow show-query.log`

## 使用 Explain 进行分析
Explain 用来分析 SELECT 查询语句，开发人员可以通过分析 Explain 结果来优化查询语句。

列名|描述
----|---
type|表示找到所需数据使用的扫描方式
select_type|查询类型，有简单查询、联合查询、子查询等
table|表名
key|使用的索引
row|扫描的行数
Extra|

常见扫描方式（type）|描述
-----------|---
system|系统表，少量数据，往往不需要磁盘 IO
const|常量连接，主键或者唯一索引上的等值查询
eq_ref|主键索引或者非空唯一索引上的 join 查询，等值扫描，对于前表的每一行，后表只有一行命中
ref|非主键非唯一索引等值扫描
range|范围扫描，如 between
index|索引树扫描，需要扫描索引上的全部数据，如 InnoDB 的count
ALL|全表扫描

Extra|描述
-----|---
Using Where|SQL 使用了 where 条件过滤数据
Using index|返回的所有列数据均在一颗所引述上，无需访问实际行记录（索引覆盖）
Using index condition|命中索引但是没有索引覆盖
using filesort|得到所需结果集，需要对所有记录进行文件排序（在没有建立索引的列上 order by）
using temporary|需要建立临时表(temporary table)来暂存中间结果(group by 和 order by 同时存在，且作用于不同的字段时）
Using join buffer(Block Nested Loop)|需要进行循环嵌套计算（两个关联表join，关联字段均未建立索引）

## profiling 分析查询
- profiling 默认是关闭的，通过`select @@profiling；`查看是否开启，通过`set profiling=1;`开启
- 执行需要测试的 sql 语句
- `show profiles\G;` 得到被执行的 SQL 语句的时间和 ID。
- `show profile for query 1;`得到 id 为 2 的语句执行的详细信息

# 优化方向
- 数据表数据类型和表结构优化
- 索引优化
- SQL语句的优化
- 存储引擎的优化
- 数据库服务器架构的优化

# 数据表数据类型和表结构优化

## 建表原则
- 定常与变长分离
- 常用字段和不常用字段分离
- 在 1 对多，需要关联统计的字段上，添加冗余字段
- 不允许 null

## 列类型选择
- 字段类型优先级 int date,time enum char varchar blob text
- 整形：定长，没有国家/地区之分，没有字符集差异，char 需要考虑字符集与校对集（排序规则）
- enum：能起约束的目的，内部用整形存储，但与 char 联查时，内部要经历串和值的转化
- varchar：不定长，速度慢
- text/blob：无法使用内存临时表（排序等操作只能在磁盘上进行）
- varchar(10) 和 varchar(300) 在表联查时花更多内存
- 尽量避免null，不利于索引，要用特殊字节标注，在磁盘占据的空间更大（mysql5.5对null做了改进）
- ip 地址的存储，用字符串浪费空间。可以用 iptolong 函数转化为整形存储 

# 索引优化

## 索引的创建原则
- 最适合索引的列是出现在where，on，group by，order by 子句中的列
- 尽量选择区分度高的列作为索引
- 对较小的数据列使用索引,这样会使索引文件更小,同时内存中也可以装载更多的索引键
- 索引基数越大效果越好
- 避免创建过多索引
- 主键尽可能选择较短的数据类型，可以有效减少索引的磁盘占用提高查询效率

## 联合索引
在需要使用多个列作为条件进行查询时，使用复合索引比使用多个单列索引性能更好。

### 左前缀原则
原理：比如，我们在（a,b,c）字段上创建一个联合索引，则索引记录会首先按照 A 字段排序，然后再按照 B 字段排序然后再是 C 字段。类似于查字典。
index(a,b,c)
where a=1 and b>7 and c=2 只能使用 a，b，b用了一半，无法使用到c（使用到范围查询和模糊查询后面的索引都用不到）
where a=1 and b=3 order by c 都能用上

## 索引与排序
- 对于覆盖索引，直接在索引上查询时，就是有序的，using index
- 在 InnoDB 引擎中，沿着索引字段排序，自然是有序的， MyISAM 引擎，如果按某索引字段排序，但取出的字段中有未索引字段的做法不是索引->回行，索引->回行，而是先取出所有行，再排序
- group by 和 order by 同时存在，且作用于不同的字段时，先取出数据，形成临时表做 filesort

## 索引覆盖
如果查询的列恰好是索引的一部分，那么查询只需要在索引文件上进行,不需要回到磁盘再找数据
如 

```sql
create index index_birthday_and_user_name on user_info(birthday, user_name);`,`select user_name from user_info where birthday = '1991-11-1'`
```



## 索引失效的情况
- ASC，DESC 混用
- WHERE 字句中出现非排序使用到的索引
- 排序列包含非同一个索引的列
- 排序列使用了复杂的表达式
- 复合索引遵循左前缀原则
- like 查询，% 在前，索引失效，可以使用全文索引
- 如果 MySQL 估计使用索引比全表扫描更慢,会放弃使用索引
- or 条件中的列有索引，后面的没有,索引不会被用到
- 负向条件(!=、<>、not in、not exists、not like 等)查询不能使用索引，可以优化为 in 查询。
- 索引列不能是表达式的一部分，也不能是函数的参数
- 建立索引的列不允许为 null，column is null 可以命中索引，is not null 不能命中。
- 列的值是字符串，查询时一定要给值加引号,否则索引失效

## 伪哈希索引
- 同时存在 url 列 hash 列，存储时 crcurl=crc32(url)

## 索引列的顺序
让选择性最强的索引列放在前面。
索引的选择性是指：不重复的索引值和记录总数的比值。最大值为 1，此时每个记录都有唯一的索引与其对应。选择性越高，查询效率也越高。

## 对较长的字段使用前缀索引
可以节约大量索引空间
对于 BLOB、TEXT 和 VARCHAR 类型的列，必须使用前缀索引，只索引开始的部分字符。
对于前缀长度的选取需要根据索引选择性来确定。

## 索引的使用条件
- 对于非常小的表、大部分情况下简单的全表扫描比建立索引更高效；
- 对于中到大型的表，索引就非常有效；
- 但是对于特大型的表，建立和维护索引的代价将会随之增长。这种情况下，需要用到一种技术可以直接区分出需要查询的一组数据，而不是一条记录一条记录地匹配，例如可以使用分区技术。

## 索引碎片与维护
- 在长期的数据更改过程中，索引文件和数据文件，都将产生空洞，形成碎片，可以通过一个不产生对数据实质影响的操作来修改表
- 或者使用 optimize table 也可以修复

## 索引的优点
- 大大减少服务器需要扫描的数据量
- 帮助服务器避免进行排序和分组，以及避免创建临时表（B+Tree 索引是有序的，可以用于 ORDER BY 和 GROUP BY 操作。临时表主要是在排序和分组过程中创建，因为不需要排序和分组，也就不需要创建临时表）。
- 将随机 I/O 变为顺序 I/O（B+Tree 索引是有序的，会将相邻的数据都存储在一起）。
- 大大提高查询速度，降低写的速度，占用磁盘

# SQL语句的优化

## 切分大查询
一个大查询如果一次性执行的话，可能一次锁住很多数据、占满整个事务日志、耗尽系统资源、阻塞很多小的但重要的查询。
将一个大的查询分解成多个小的相同的查询。
一次性删除 1000 万的数据要比一次删除 1 万，暂停一会儿的方案更损耗服务器开销。
```sql
DELETE FROM messages WHERE create < DATE_SUB(NOW(), INTERVAL 3 MONTH);
//切分后
rows_affected = 0
do {
rows_affected = do_query(
"DELETE FROM messages WHERE create < DATE_SUB(NOW(), INTERVAL 3 MONTH) LIMIT 10000")
} while rows_affected > 0
```

## 分解关联查询
将一个关联查询分解成对每一个表进行一次单表查询，然后在应用程序中进行关联。
- 让缓存更高效。对于连接查询，如果其中一个表发生变化，那么整个查询缓存就无法使用。而分解后的多个查询，即使其中一个表发生变化，对其它表的查询缓存依然可以使用。
- 分解成多个单表查询，这些单表查询的缓存结果更可能被其它查询使用到，从而减少冗余记录的查询。
- 减少锁竞争。
- 在应用层进行连接，可以更容易对数据库进行拆分，从而更容易做到高性能和可伸缩。
- 查询本身效率也可能会有所提升。

## 优化特定类型的查询语句
- count(*) 会忽略所有列，直接统计所有列数，不要使用 count(列名)。count()在查询 < 时快，查询 >100 时可以用总的减去 <=100。
- group by 和 distinct 可以使用索引来优化
- 如果不需要使用 order by 进行 group by 时使用 order by null，MySQL 就不会再进行文件排序
- 优化关联查询：确定 on 或者 using 子句的列上有索引，确保 group by 和 order by 中只有一个表中的列，这样 MySQl 才有可能使用索引
- 优化子查询：用关联查询替代
- 优化 limit 分页：limit 偏移量大的时候，查询效率较低。 优化方法：
    1. 从业务上解决，不允许翻过 100 页
    1. 不用 offset，用条件查询,记录上次查询最大 ID，下次查询时直接根据 ID 查询
- 优化 UNION 查询：UNION ALL 效率更高，会查询出重复的数据，但是可以在应用层筛选。 UNION 的子句要尽量具体，查询更少的行
- union、in、or 都能够命中索引，建议使用 in。
- min/max 优化

## 优化数据访问
- 改变数据库和表的结构，修改数据表的范式，冗余
- 只返回必要的列：最好不要使用 SELECT * 语句。
- 只返回必要的行：使用 LIMIT 语句来限制返回的数据。
- 缓存重复查询的数据：使用缓存可以避免在数据库中进行查询，特别在要查询的数据经常被重复查询时，缓存带来的查询性能提升将会是非常明显的。
- 使用索引来覆盖查询

# 参考文章
- 燕十八.MySQL 优化视频
- [同一个SQL语句，为啥性能差异咋就这么大呢？](https://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=2651962514&idx=1&sn=550c48c9395b52b7ec561741e86e5ce0&chksm=bd2d094e8a5a80589117a653a30d062b5760ec20f8ab9e2154a63ab782d3353d1b1da50b9bc2&scene=21#wechat_redirect)
- [如何利用工具，迅猛定位低效SQL？](https://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=2651962587&idx=1&sn=d197aea0090ce93b156e0774c6dc3019&chksm=bd2d09078a5a801138922fb5f2b9bb7fdaace7e594d55f45ce4b3fc25cbb973bbc9b2deb2c31&scene=21#wechat_redirect)