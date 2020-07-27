---
title: MySQL 分库分表
date: 2019-04-12 23:02:28
tags: 
    - MySQL
category: 基础
---

# 分库分表

## 分片（水平分割）

水平切分又称为 Sharding，它是将同一个表中的记录拆分到多个结构相同的表中。
当一个表的数据不断增多时，Sharding 是必然的选择，它可以将数据分布到集群的不同节点上，从而缓存单个数据库的压力。

### 水平切分，到底是分库还是分表

建议分库

- 分表依然公用一个数据库文件，仍然有磁盘 IO 的竞争
- 分库能够很容易的将数据迁移到不同数据库实例，甚至数据库机器上，扩展性更好

### Sharding 策略

- 哈希取模：hash(key) % N；
  - 没有热点问题，单扩容迁移数据痛苦
- 范围：可以是 ID 范围也可以是时间范围；
- 不需要迁移数据，但有热点问题
- 映射表：使用单独的一个数据库来存储映射关系。

Sharding 存在的问题：

1. 事务问题：使用分布式事务来解决，比如 XA 接口。
1. 连接：可以将原来的连接分解成多个单表查询，然后在用户程序中进行连接。
1. ID 唯一性
   - 使用全局唯一 ID（GUID）
   - 为每个分片指定一个 ID 范围
   - 分布式 ID 生成器 (如 Twitter 的 Snowflake 算法)

### 解决的问题

- 线性提升数据库写性能，需要注意的是，分组架构是不能线性提升数据库写性能的
- 降低单库数据容量

### 缺点

给应用增加复杂度，通常查询时需要多个表名，查询所有数据都需要 UNION 操作

## 垂直切分

垂直切分是将一张表按列切分成多个表，通常是按照列的关系密集程度进行切分，也可以利用垂直切分将经常被使用的列和不经常被使用的列切分到不同的表中。

在数据库的层面使用垂直切分将按数据库中表的密集程度部署到不同的库中，例如将原来的电商数据库垂直切分成商品数据库、用户数据库等。

### 使用场景

- 一个表中某些列常用，有些不常用
- 可以使数据行变小，一个数据页能存储更多数据，查询时减少 I/O 次数

### 缺点

管理冗余咧，查询所有数据需要 JOIN 操作

## 分区

所有数据，逻辑上还在一个表中，但物理上，可以根据一定的规则放在不同的文件中。这是 MySQL5.1 之后支持的功能，业务代码无需改动。

### 工作原理

创建表时使用 partition by 子句定义每个分区存放的数据，执行查询时，优化器会根据分区定义过滤那些没有我们需要数据的分区，这样查询只需要查询所需要数据在的分区即可

### 分区的使用场景

- 表非常大，无法全部存在内存，或者只在表的最后有热点数据，其他都是历史数据
- 分区表的数据更易维护，可以对独立的分区进行独立的操作
- 分区表的数据可以分布在不同的机器上，从而高效利用资源
- 可以备份和恢复独立的分区

### 限制

- 一个表最多有 1024 个分区
- 5.1 版本中，分区表表达式必须是整数，5.5 可以使用列分区
- 分区字段中如果有主键和唯一索引列，那么主键和唯一索引列必须包含进来
- 分区表中无法使用**外键**索引

# 分布式ID生成方案

分库分表后，使用自增字段无法保证 ID 的全局唯一性。

UUID 虽然可以实现全局唯一性，但是生成的 ID 需要有序，因为 ID 可能成为排序的字段。

1. 数据库自增ID：用一个数据库建立一张表来生成 ID，使用主主模式，设置初始值和步长（MySQL1：1357，MySQL：2468）
1. 号段模式
1. Redis 生成 ID：incr 和 incrBy 自增原子命令
1. 雪花算法
1. 

可以基于 Snowflake 算法搭建发号器，Snowflake 满足 ID 所需的全局唯一性，单调递增性，还包含一定的业务上的意义。

能让负责生成分布式 ID 的每台机器在每毫秒内生成不一样的 ID。

![](https://gitee.com/cellophane/image/raw/master/20200412232622.png)



# 什么是分布式中间件

数据库分布式中间件是一款专门针对分布式数据库集群而设计的中间件软件，它是以 MySQL 为底层存储来实现关系型数据的统一管理与访问的平台。分布式中间件可将应用发起的 SQL 语句经过解析、转换、再根据路由算法下推到集群系统中的各个MySQL数据库节点上，MySQL 数据库节点获得了正确且能够有效执行的分段 SQL 语句，执行之后将结果返还到中间件，由中间件对查询结果进行汇总、计算、合并成应用服务所需的执行结果。

从最终用户的角度，分布式中间件可以认为就是一个传统的关系型数据库，支持通过标准的 SQL 语句进行数据操作，对前端业务系统来说，可以大幅降低开发难度，提升开发速度。用户可以将表定义为任何一种中间件支持的存储方式，比如Innodb。

# 分布式中间件
引入分布式中间件的目的是为了解决高并发+海量数据存储的问题。

## 直连数据库模式
![](https://gitee.com/cellophane/image/raw/master/db-1.png)
优点：

- 中间消耗最低，对于低并发，少量存储来说，请求响应时间极短。
- 由于调用链简单，排障比较容易。

缺点：
- 无法承载高并发访问
- 无法承载海量数据

## 读写分离模式
![](https://gitee.com/cellophane/image/raw/master/db-2.png)
优点：解决了高并发问题

缺点：
- 增大了应用到存储之间的距离，无法获取最优的请求响应时间
- 无法解决海量数据存储问题

## 数据分片模式
![](https://gitee.com/cellophane/image/raw/master/db-3.png)
优点：

- 解决了高并发的问题
- 解决了海量存储的问题

缺点：
- 增大了应用到存储之间的距离，无法获取最优的请求响应时间
- 加大运维难度
- 分布式事务管理难度增加

### 分库分表后的问题
- 分布式事务问题
- 表关联问题
- 分页、排序比较复杂
- 需要生产全局唯一主键，叫做分布式 ID。

# 是否分库分表
如何计算当前表的理论上分表的最大值

在开始这一块之前，我们需要了解以下几个信息：
1. 每一个节点在数据库中都是一个页，页是 innodb 磁盘管理最小的单位，innodb 每个页的大小是 16K，且不可更改。常见的类型有：数据页 B-tree Node；undo 页 Undo Log Page；系统页 System Page；事务数据页 Transaction system Page；插入缓冲位图页 Insert Buffer Bitmap；插入缓冲空闲列表页 Insert Buffer freeBitmap；未压缩的二进制大对象页Uncompressed BLOB Page；压缩的二进制大对象页 Compressed BLOB Page。
1. 每个key后有个页号4B，还有6B的其他数据（参考《MySQL技术内幕：InnoDB存储引擎》P193的页面数据）
1. 装载因子（InnoDB 默认为15/16）

以下是针对单行数据在 300bit 大小的数据计算步骤：

1. 索引节点：`(16000/(8+4+6))*15/16=833.33`个
1. 叶子节点：假设单行数据`1218byte`,计算为`(16000/（1218*8+8+4+6）)*15/16=1.54`
1. 总共三层B+树可存储数据量为：`833.33*833.33*1.54=1067054`条数据

所以一个表的数据在 1067054 条时，B+ 树会膨胀到四层，影响 sql 的性能（可能数据库有会压缩数据之类的优化操作，但是数量级应该不会差很多）

# 手动分表的方式

## 根据取值范围
可以根据时间区间或者 ID 区间来切分。

### 优点
- 单表数据量可控
- 水平扩展只需要增加节点即可，无需对其他分片的数据进行迁移

### 缺点
由于连续分片可能存在数据热点，如果按时间字段分片，有些分片存储最近时间段内的数据，可能会被频繁的读写，而有些分片存储的历史数据，则很少被查询。

## 哈希取模
数据分片相对比较均匀，不易出现某个库并发访问的问题。

例如：以 messageEntry 表的 to_uid 字段为依据，除 1024 取余作为后缀。
`$tableName = "messageEntry". messageEntry['to_uid']% 1024;`
消息模块 messageEntry_ 表根据 user_id 手动将表分为 1024 张表，使用表前自己根据计算规则`(user_id % 1024)`计算得到需要访问的表名。

# 使用中间件分表
1. sql 解析
1. 路由：提前配置分库分表规则，给业务方提供一个虚 ip:port, 业务方不再感知分库分表规则。
1. 组装数据：将返回结果根据需求组装后返回给业务方。

## 从手动分表改造成中间件分表的步骤
1. DBA 配置中间件
1. 表 messageEntry 按照原有分表方式(按照 user_id 分表标准)、表总数(仍 1024 张)等不变,
1. 给业务方一个虚 ip，业务方使用虚 ip 统一访问 messageEntry 表，不再直接操作具体一张表(messageEntry_xx).
1. 中间过渡(支持两种连接方式，既可以根据规则计算得到表名，也可以通过中间件方式路由到真实访问的表)
1. 业务方修改读写的 table: 将原来的使用的 messageEntry_xxx 统一替换为 messageEntry。此后，业务方不再感知分表分库等细节。

sharding-JDBC 在后端将数据量较大的数据表水平拆分到后端的每个 MySQL 数据库中，这些拆分到 MySQL 中的数据库被称为分库，分库中的表称为分表。

拆分后，每个分库负责每一份数据的读写操作，从而有效的分散了整体访问压力。在系统扩容时，只需要水平增加分库的数量，并且迁移相关数据，就可以提高 MySQL 系统的总体容量。

# Sharding-JDBC简介
Sharding-JDBC 是当当应用框架 ddframe 中，从关系型数据库模块 dd-rdb 中分离出来的数据库水平分片框架，实现透明化数据库分库分表访问。Sharding-JDBC 是继 dubbox 和 elastic-job 之后，ddframe 系列开源的第3个项目。

**Sharding-JDBC 直接封装 JDBC API，可以理解为增强版的 JDBC 驱动，旧代码迁移成本几乎为零**。

可适用于任何基于 Java 的 ORM 框架，如 JPA、Hibernate、Mybatis、Spring JDBC Template 或直接使用 JDBC。
可基于任何第三方的数据库连接池，如 DBCP、C3P0、 BoneCP、Druid 等。
Sharding-JDBC 定位为轻量 Java 框架，使用客户端直连数据库，以 jar 包形式提供服务，无 proxy 代理层，无需额外部署，无其他依赖，DBA 也无需改变原有的运维方式。

Sharding-JDBC 分片策略灵活，可支持等号、between、in 等多维度分片，也可支持多分片键。

SQL解析功能完善，支持聚合、分组、排序、limit、or 等查询，并支持 Binding Table 以及笛卡尔积表查询。

## 与常见开源产品对比
Cobar 属于中间层方案，在应用程序和 MySQL 之间搭建一层 Proxy。中间层介于应用程序与数据库间，需要做一次转发，而基于JDBC协议并无额外转发，直接由应用程序连接数据库，性能上有些许优势。这里并非说明中间层一定不如客户端直连，除了性能，需要考虑的因素还有很多，中间层更便于实现监控、数据迁移、连接管理等功能。

Cobar-Client、TDDL 和 Sharding-JDBC 均属于客户端直连方案。此方案的优势在于轻便、兼容性、性能以及对 DBA 影响小。其中 Cobar-Client 的实现方式基于 ORM（Mybatis）框架，其兼容性与扩展性不如基于 JDBC 协议的后两者。

## 实现原理
前文已介绍了 Sharding-JDBC 是实现了 JDBC 协议的 jar 文件。基于 JDBC 协议的实现与基于 MySQL 等数据库协议实现的中间层略有差别。

无论使用哪种架构，核心逻辑均极为相似，除了协议实现层不同（JDBC或数据库协议），都会分为分片规则配置、SQL 解析、SQL 改写、SQL 路由、SQL 执行以及结果归并等模块。

![](https://gitee.com/cellophane/image/raw/master/sharding.png)

## 分片规则配置
Sharding-JDBC 的分片逻辑非常灵活，支持分片策略自定义、复数分片键、多运算符分片等功能。

如：根据用户 ID 分库，根据订单 ID 分表这种分库分表结合的分片策略；或根据年分库，月份 + 用户区域 ID 分表这样的多片键分片。

Sharding-JDBC 除了支持等号运算符进行分片，还支持 in/between 运算符分片，提供了更加强大的分片功能。

## SQL 解析
SQL 解析作为分库分表类产品的核心，性能和兼容性是最重要的衡量指标。目前常见的SQL解析器主要有 fdb/jsqlparser 和 Druid。Sharding-JDBC 使用 Druid 作为 SQL 解析器，经实际测试，Druid 解析速度是另外两个解析器的几十倍。

目前 Sharding-JDBC 支持 join、aggregation（包括avg）、order by、 group by、limit 甚至 or 查询等复杂 SQL 的解析。目前不支持 union、部分子查询、函数内分片等不太应在分片场景中出现的 SQL 解析。

## SQL改写
SQL改写分为两部分，一部分是将分表的逻辑表名称替换为真实表名称。另一部分是根据 SQL 解析结果替换一些在分片环境中不正确的功能。这里具两个例子：
1. avg计算。在分片的环境中，以 `avg1 +avg2+avg3/3` 计算平均值并不正确，需要改写为`(sum1+sum2+sum3)/(count1+count2+ count3)`。这就需要将包含 `avg` 的 `SQL` 改写为 `sum` 和 `count`，然后再结果归并时重新计算平均值。
1. 分页。假设每 10 条数据为一页，取第 2 页数据。在分片环境下获取 `limit 10, 10`，归并之后再根据排序条件取出前 10 条数据是不正确的结果。正确的做法是将分条件改写为 `limit 0, 20`，取出所有前 2 页数据，再结合排序条件算出正确的数据。可以看到越是靠后的 Limit 分页效率就会越低，也越浪费内存。有很多方法可避免使用 limit 进行分页，比如构建记录行记录数和行偏移量的二级索引，或使用上次分页数据结尾 ID 作为下次查询条件的分页方式。

## SQL路由
SQL 路由是根据分片规则配置，将 SQL 定位至真正的数据源。主要分为单表路由、Binding 表路由和笛卡尔积路由。

单表路由最为简单，但路由结果不一定落入唯一库（表），因为支持根据 between 和 in 这样的操作符进行分片，所以最终结果仍然可能落入多个库（表）。

Binding 表可理解为分库分表规则完全一致的主从表。举例说明：订单表和订单详情表都根据订单 ID 作为分片键，任意时刻分片逻辑均相同。这样的关联查询和单表查询难度和性能相当。

笛卡尔积查询最为复杂，因为无法根据 Binding 关系定位分片规则的一致性，所以非 Binding 表的关联查询需要拆解为笛卡尔积组合执行。查询性能较低，而且数据库连接数较高，需谨慎使用。

## SQL执行
路由至真实数据源后，Sharding-JDBC 将采用多线程并发执行 SQL，并完成对 addBatch 等批量方法的处理。

## 结果归并
结果归并包括 4 类：普通遍历类、排序类、聚合类和分组类。每种类型都会先根据分页结果跳过不需要的数据。

普通遍历类最为简单，只需按顺序遍历 ResultSet 的集合即可。

排序类结果将结果先排序再输出，因为各分片结果均按照各自条件完成排序，所以采用归并排序算法整合最终结果。

聚合类分为3种类型，比较型、累加型和平均值型。比较型包括 max 和 min，只返回最大（小）结果。累加型包括 sum 和 count，需要将结果累加后返回。平均值则是通过 SQL 改写的 sum 和 count 计算，相关内容已在 SQL 改写涵盖，不再赘述。

分组类最为复杂，需要将所有的 ResultSet 结果放入内存，使用 map-reduce 算法分组，最后根据排序和聚合条件做相关处理。最消耗内存，最损失性能的部分即是此，可以考虑使用 limit 合理的限制分组数据大小。

结果归并部分目前并未采用管道解析的方式，之后会针对这里做更多改进。 

## Sharding-JDBC 具体使用
先做一个最简单的试用，不做分库，仅做分表。选择数据表 operate_history，这个数据表记录所有的操作历史，是整个系统中数据量最大的一个数据表。

希望将这个表拆分为四个数据表，分别是 operate_history_0、operate_history_1、operate_history_2 、operate_history_3。数据能够分配保存到四个数据表中，降低单表的数据量。同时，为了尽量减少跨表的查询操作，决定使用字段 entity_key 为分表依据，这样同一个 entity 对象的所有操作，将会记录在同一个数据表中。

以下是针对JPA项目的修改过程。其他项目请参考官方网站的文档。

1. 修改 pom.xml 增加 dependency，需要添加两个 jar，sharding-jdbc-core 和 sharding-jdbc-config-spring。

```xml
<dependency>

  <groupId>com.dangdang</groupId>

 <artifactId>sharding-jdbc-core</artifactId>

  <version>1.3.0</version>

</dependency>

<dependency>

  <groupId>com.dangdang</groupId>

  <artifactId>sharding-jdbc-config-spring</artifactId>

  <version>1.3.0</version>

</dependency>
```
2. 修改 Spring 中 Database 部分的配置

原 Database 配置
```xml
<bean id="dataSource"class="org.apache.tomcat.jdbc.pool.DataSource"destroy-method="close">

  <propertyname="driverClassName"value="com.mysql.jdbc.Driver"></property>

  <propertyname="url" value="jdbc:mysql://localhost:3306/sharding"></property>

  <propertyname="username" value="root"></property>

  <propertyname="password" value="sharding"></property>

</bean>
```
修改后的配置
```xml
<beanid="db-node-0"class="org.apache.tomcat.jdbc.pool.DataSource"destroy-method="close">

 <property name="driverClassName"value="com.mysql.jdbc.Driver"></property>

 <property name="url"value="jdbc:mysql://localhost:3306/sharding"></property>

 <property name="username"value="root"></property>

 <property name="password"value="sharding"></property>

</bean>

<rdb:strategyid="historyTableStrategy"

 sharding-columns="entity_key"

 algorithm-class="cn.codestory.sharding.SingleKeyTableShardingAlgorithm"/>

<rdb:data-sourceid="dataSource">

 <rdb:sharding-ruledata-sources="db-node-0"default-data-source="db-node-0">

  <rdb:table-rules>

   <rdb:table-rulelogic-table="operate_history"

   actual-tables="operate_history_0,operate_history_1,operate_history_2,operate_history_3"

   table-strategy="historyTableStrategy" />

  </rdb:table-rules>

 </rdb:sharding-rule>

</rdb:data-source>
```
3. 编写类 SingleKeyTableShardingAlgorithm，这个类用来根据 entity_key 值确定使用的分表名。参考 sharding 提供的示例代码进行修改。核心代码如下

```java
public Collection<String> doInSharding(Collection<String> availableTargetNames, ShardingValue<Long> shardingValue) {
    //这是一个简单的实现，对 entity_key 进行求模，用余数确定数据表名。
    int targetCount = availableTargetNames.size();
    Collection<String> result = newLinkedHashSet <>(targetCount);
    Collection<Long> values = shardingValue.getValues();
    for (Long value : values) {
        for (String tableNames : availableTargetNames) {
            if (tableNames.endsWith(value % targetCount + "")) {
                result.add(tableNames);
            }
        }
    }
    return result;
}
```
4. 修改主键生成方法：因为数据分表保存，不能使用 identify 方式生成数据表主键。如果主键是 String 类型，可以考虑使用 uuid 生成方法，但它查询效率会相对比较低。如果使用 long 型主键，可以使用其他方式，一定要确保各个子表中的主键不重复。
5. 历史数据的处理：根据数据分表的规则，需要对原有数据包的数据进行迁移，分别移动到四个数据表中。如果不做这一步，或者数据迁移到了错误的数据表，后续将会查询不到这些数据。
6. 至此，对项目的修改基本完成，重新启动项目并增加 operate_history 数据，就会看到新添加的数据，已经根据我们的分表规则，插入到了某一个数据表中。查询的时候，能够同时查询到多个实际数据表中的数据。

## 数据分表规则的一些考虑
前面的例子，演示的是根据 entity_key 进行分表，也可以使用其他字段如主键进行分表。
- 根据主键进行分配：这种方式能够实现最平均的分配方法，每生成一条新数据，会依次保存到下一个数据表中。
- 根据用户ID进行分配：这种方式能够确保同一个用户的所有数据保存在同一个数据表中。如果经常按用户id查询数据，这是比较经济的一种做法。
- 根据某一个外键的值进行分配：前面的例子采用的就是这种方法，因为这个数据可能会经常根据这个外键进行查询。
- 根据时间进行分配：适用于一些经常按时间段进行查询的数据，将一个时间段内的数据保存在同一个数据表中。比如订单系统，缺省查询一个月之内的数据。

# 参考文章
- [深度认识 Sharding-JDBC：做最轻量级的数据库中间层](https://juejin.im/entry/5905ac37a22b9d0065e1199c)
- [数据库架构设计中，最重要的“基概”！！！](https://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=2651963031&idx=1&sn=f52b724ff33ef008668ef9165c51d39b&chksm=bd2d0b4b8a5a825df41252dd381d0206e141f6a54f253086030113f14584bb11612308ce8e16&scene=21#wechat_redirect)
- [分布式id生成方案总结](https://juejin.im/post/5d6fc8eff265da03ef7a324b)
