---
title: Redis-应用
date: 2019-11-16 22:03:29
tags: Redis
categories: 中间件
---
# 为什么选择 Redis

## Redis 的优势

- 速度快，因为数据存在内存中
- 支持丰富的数据类型
- 支持事务
- 可以持久化数据
- 支持 master-salve 模式的数据备份 

## Redis 与 Memchche 的区别
- 性能相差不大
- Redis 在 2.0 版本后增加了自己的 VM 特性，突破物理内存的限制。Memcache 可以修改最大可用内存，采用 LR 算法
- Redis 以来客户端实现分布式读写 Memcache 本身没有数据冗余机制
- Redis 支持快照、AOF，依赖快照进行持久化，AOF 增强了可靠性的同时，对性能有所影响
- Memcache 不支持持久化，通常做缓存，提升性能
- Memcachche 在并发场景下，用 CAS 保证一致性，redis事务支持比较弱，只能保证事务中的每个操作连续行
- Redis 支持多种数据类型
- Redis 用于数据量较小的高性能操作和运算上
- Memcache 用于在动态系统中减少数据库负载，提升性能

## 数据类型的应用场景
- String：一般做一些复杂计数功能的缓存
- hash：存放的是结构化的对象，方便的用来操作其中某个字段，可以用来存储用户信息。
- list：可以做简单的消息队列，可以利用 lrange 做基于 Redis 的分页功能。
- set：做全局的去重功能，或者利用交集、并集、差集计算共同爱好，全部的爱好，自己独有的爱好。
- zset：可以做排行榜应用，取 Top N 操作，还可以用来做延时任务，范围查找。

# 高可用

高可用相关技术：

- 持久化：持久化是最简单的高可用方法(有时甚至不被归为高可用的手段)，主要作用是数据备份，即将数据存储在硬盘，保证数据不会因进程退出而丢失。
- 复制：复制是高可用 Redis 的基础，哨兵和集群都是在复制基础上实现高可用的。复制主要实现了数据的多机备份，以及对于读操作的负载均衡和简单的故障恢复。缺陷：故障恢复无法自动化；写操作无法负载均衡；存储能力受到单机的限制。
- 哨兵：在复制的基础上，哨兵实现了自动化的故障恢复。缺陷：写操作无法负载均衡；存储能力受到单机的限制。
- 集群：通过集群，Redis解决了写操作无法负载均衡，以及存储能力受到单机限制的问题，实现了较为完善的高可用方案。

## 持久化

持久化的功能：Redis是内存数据库，数据都是存储在内存中，为了避免进程退出导致数据的永久丢失，需要定期将Redis中的数据以某种形式(数据或命令)从内存保存到硬盘；当下次Redis重启时，利用持久化文件实现数据恢复。除此之外，为了进行灾难备份，可以将持久化文件拷贝到一个远程位置。

RDB 做镜像全量持久化，AOF 做增量持久化。两者会配合来使用。redis 重启时，会使用 RDB 持久化文件重新构建内存，再使用 AOF 重放近期操作指令来完整恢复。

### RDB

RDB 会将当前进程中的数据生成快照保存到硬盘，生成多个数据文件，每个数据文件分别都代表了某一时刻 Redis 里面的数据，保存的文件后缀是 rdb，当 Redis 重启时，可以读取快照文件恢复数据。

RDB 持久化的触发分为手动触发和自动触发。

- 手动触发：save 命令和 bgsave 命令都可以生成 RDB 文件
  -  save 命令会阻塞 Redis 服务器进程，直到 RDB 文件创建完毕为止，在 Redis 服务器阻塞期间，服务器不能处理任何命令请求。
  - bgsave 命令会创建一个子进程，由子进程来负责创建 RDB 文件，父进程(即 Redis 主进程)则继续处理请求。
- 自动触发：在配置文件中通过`save m n`，指定 m 秒发生 n 次变化时触发 bgsave。 

### AOF

AOF 把每条写入的命令存入日志，以 append-only 的模式写入一个日志文件中，因为这个模式是只追加的方式，所以没有任何磁盘寻址的开销，所以很快，有点像 Mysql 中的 binlog。

一般使用定时 sync，比如 1 秒 1 次，这个时候最多就会丢失 1s 的数据。

## 主从复制

主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点(master)，后者称为从节点(slave)；数据的复制是单向的，只能由主节点到从节点。

### **主从复制的作用**

- 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
- 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余。
- 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务（即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点），分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量。
- 高可用基石：主从复制还是哨兵和集群能够实施的基础，因此说主从复制是Redis高可用的基础。

Redis Sentinal 着眼于高可用，在 master 宕机时会自动将 slave 提升为 master，继续提供服务。

Redis Cluster 着眼于扩展性，在单个 redis 内存不足时，使用 Cluster 进行分片存储。

### 主从复制步骤

在本机上模拟主从复制可以通过复制配置文件，在不同端口打开 Redis 实例来模拟主从复制。

#### 配置多个端口的 Redis 实例

1. 复制 Redis 配置文件，修改 port。
1. 输入命令 `sudo redis-server /etc/redis/redis6380.conf` 启动 Redis。
1. 输入命令 `redis-cli -p 6380` 进入 Redis 实例。

#### 配置主从复制

**主从复制的开启，完全是在从节点发起的；不需要我们在主节点做任何事情。**

有三种方式开启主从复制

- 在从服务器的配置文件中加入：`slaveof <masterip> <masterport>`
- redis-server 启动命令后加入 `--slaveof <masterip> <masterport>`
- Redis 服务器启动后，直接通过客户端执行命令：`slaveof <masterip> <masterport>`，则该Redis实例成为从节点。

如：在 6380 端口的 Redis 实例输入命令 `slaveof 17.0.0.1 6379` ，6380 的 Redis 就成为了 6379 Redis 的从节点。

#### 断开复制

从节点输入 `slaveof no one` 就可以断开复制。

### 实现原理

## 哨兵（Sentinel）

哨兵是基于 Redis 主从复制，主要作用是**解决主节点故障恢复的自动化问题**，进一步提高系统的高可用性。

- 监控（Monitoring）：哨兵会不断地检查主节点和从节点是否运作正常。
- 自动故障转移（Automatic failover）：当主节点不能正常工作时，哨兵会开始自动故障转移操作，它会将失效主节点的其中一个从节点升级为新的主节点，并让其他从节点改为复制新的主节点。
- 配置提供者（Configuration provider）：客户端在初始化时，通过连接哨兵来获得当前Redis服务的主节点地址。
- 通知（Notification）：哨兵可以将故障转移的结果发送给客户端。

![哨兵架构图](https://gitee.com/cellophane/image/raw/master/20200402235846.png)

哨兵架构由两部分组成：

- 哨兵节点：哨兵系统由一个或多个哨兵节点组成，**哨兵节点是特殊的 redis 节点**，不存储数据。
- 数据节点：主节点和从节点都是数据节点。

哨兵进程是用于**监控 Redis 集群中 Master 主服务器工作的状态**。

在 Master 主服务器发生故障的时候，可以实现 Master 和 Slave 服务器的切换，保证系统的高可用（High Availability）

哨兵至少要用**三个实例**去保证自己的健壮性。

### 实现步骤

包含 1 个主节点，2 个从节点和 3 个哨兵节点。

1. 部署主从节点，参考主从复制步骤。
1. 节点启动后，列检主节点查看主从状态是否正常`info replication`。
1. 在哨兵节点的配置中添加 `sentinel monitor mymaster 127.0.0.1 6379 2`，该哨兵节点监控192.168.92.128:6379这个主节点，该主节点的名称是 mymaster，最后的 2 的含义与主节点的故障判定有关：至少需要2个哨兵节点同意，才能判定主节点故障并进行故障转移。

## 集群（Cluster）

Redis Cluster是社区版推出的Redis分布式集群解决方案，主要解决Redis分布式方面的需求，比如，当遇到单机内存，并发和流量等瓶颈的时候，Redis Cluster能起到很好的负载均衡的目的。

Redis Cluster集群节点最小配置6个节点以上（3主3从），其中主节点提供读写操作，从节点作为备用节点，不提供请求，只作为故障转移使用。

分布式数据库把整个数据按分区规则映射到多个节点，即把数据划分到多个节点上，每个节点负责整体数据的一个子集。比如我们库有900条用户数据，有3个redis节点，将900条分成3份，分别存入到3个redis节点。

集群的作用有两点：

- 数据分区：集群将数据分散到多个节点，一方面突破了Redis单机内存大小的限制，存储容量大大增加；另一方面每个主节点都可以对外提供读服务和写服务，大大提高了集群的响应能力。
- 高可用：集群支持主从复制和主节点的自动故障转移（与哨兵类似）；当任一节点发生故障时，集群仍然可以对外提供服务。

[集群搭建步骤](https://www.cnblogs.com/kismetv/p/9853040.html)

# 缓存数据不一致

## 先删除缓存，再更新数据库

![](https://gitee.com/cellophane/image/raw/master/20200414172723.png)

这个是逻辑是错误的。两个并发操作，一个是更新操作，另一个是查询操作，更新操作删除缓存后，查询操作没有命中缓存，先把老数据读出来后放到缓存中，然后更新操作更新了数据库。于是，在缓存中的数据还是旧的数据，导致缓存中的数据一直是旧数据。

## 先更新数据库，再删除缓存（Cache Aside Pattern）

![](https://gitee.com/cellophane/image/raw/master/20200414172927.png)

这种情况发生的不一致，是因为缓存突然失效了。而且还要保证请求B更新操作 比 请求A的查询操作还要快；才会导致不一致。**这种情况概率会很少。一般要求不高的项目可以采用此方式**。

## 采用延时双删策略

- 先删除缓存
- 再写数据库
- 休眠 500 毫秒（时间根据自己项目的读业务逻辑的耗时，目的是确保读请求结束，写请求可以删除读请求造成的缓存脏数据）
- 再次删除缓存

## 异步更新缓存（基于订阅 binlog 的同步机制）

![](https://gitee.com/cellophane/image/raw/master/20200414171925.png)

MySQL binlog增量订阅消费+消息队列+增量数据更新到redis

- 读 Redis：热数据基本都在 Redis
- 写 MySQL:增删改都是操作 MySQL
- 更新 Redis 数据：读取 binlog 后分析 ，利用消息队列，推送更新各台的 redis 缓存数据。

# 缓存雪崩
数据未加载到缓存中，或者缓存同一时间大面积失效，导致所有请求都去查数据库，导致数据库和 CPU 和内存负载过高。

解决方式：
- 在批量往 Redis 存数据的时候，把每个失效时间都加个随机值。
- 缓存降级：利用本地缓存暂时支持，对源服务器访问进行限流，资源隔离（熔断）。
- Redis 备份和快速缓存预热。
- 提前演练：在项目上线前，演练缓存层宕掉后，应用以及后端的负载情况以及可能出现的问题，对高可用提前预演，发现问题。
- 缓存预热：系统上线后，将相关的缓存数据直接加载到缓存系统。

# 缓存穿透
指查询一个不存在的数据，从缓存 Redis 没有命中，需要从 MySQL 数据库查询，查不到数据不写入缓存，导致这个不存则的数据每次请求都要数据库查询。

解决方法：
- 如果查询数据库为空，直接设置一个默认值存放到缓存。
- 给 key 设置一些格式规则，查询之前先过滤掉不符合规则的 key
- 布隆过滤器

## 布隆过滤器
当一个元素被加入集合时，通过 K 个散列函数将这个元素映射成一个位数组中的 K 个点，把他们置为 1。检索时，只要看看这些点是不是都是 1 就知道集合里有没有它。

缺点：存在误判、删除困难。

# 参考资料
- [【原创】分布式之redis复习精讲](https://www.cnblogs.com/rjzheng/p/9096228.html)
- [深入学习Redis（3）：主从复制](https://www.cnblogs.com/kismetv/p/9236731.html)
- [缓存更新的套路](https://coolshell.cn/articles/17416.html)
- [JAVA 拾遗 — CPU Cache 与缓存行](https://www.cnkirito.moe/cache-line/)
- [你知道如何更新缓存吗？如何保证缓存和数据库双写一致性？](https://www.toutiao.com/i6669945231242166791/?group_id=6669945231242166791)
- [缓存架构 一篇足够？](https://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=2651961368&idx=1&sn=82a59f41332e11a29c5759248bc1ba17&chksm=bd2d0dc48a5a84d293f5999760b994cee9b7e20e240c04d0ed442e139f84ebacf608d51f4342&scene=21#wechat_redirect)