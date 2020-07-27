---
title: Redis-基本操作
date: 2019-11-03 21:42:17
tags: Redis
categories: 中间件
---

- redis-benchmark 性能测试工具
- redis-check-aof 日志文件检测工具（比如断电造成日志损坏，可以检测并修复）
- redis-check-dump 快照文件检测工具
- redis-cli  客户端
- redis-server 服务端

# 数据库

| 命令                   | 作用                                                         | 说明                                                         |
| :--------------------- | :----------------------------------------------------------- | ------------------------------------------------------------ |
| del key [key...]       | 删除1个或多个键                                              | 不存在的 key 忽略掉，返回真正删除的 key 的数量               |
| rename key newkey      | 给 key 赋一个新的 key 名                                     | 如果 newkey 已存在，则 newkey 的原值被覆盖                   |
| renamenx key newkey    | 当且仅当 newkey 不存在时，把 key 改名为 newkey（nx：not exists） | 发生修改返回 1，未发生修改返回 0                             |
| move key db            | 将当前数据库的 key 移动到给定的数据库 db 当中。              | 如果当前数据库和给定数据库有相同名字的 key，或者当前数据库不存在 key，则没有任何效果。 |
| randomkey              | 返回随机 key                                                 | 如果数据库为空，返回 nil                                     |
| exists key             | 判断 key 是否存在                                            | 存在返回 1，不存在返回 0                                     |
| type key               | 返回 key 存储的值的类型                                      | 返回`none`/`string`/`list`/`set`/`zset`/`hash`/`stream`      |
| dbsize                 | 返回当前数据库 key 的数量                                    |                                                              |
| keys pattren           | 查询所有符合给定模式 pattern 的 key                          | 有`* ? []` 三个通配符，* 匹配任意多个字符、? 匹配单个字符、[] 匹配括号内的某1个字符 |
| scan/sscan/hscan/zscan | 增量式迭代数据库中的键                                       | [详细用法](http://redisdoc.com/database/scan.html)           |
| sort                   | 返回或保存给定列表、集合、有序集合 `key` 中经过排序的元素。  | [详细用法](http://redisdoc.com/database/sort.html)           |
| flushdb                | 清空当前数据库中所有 key                                     |                                                              |
| flushall               | 清空所有数据库中所有 key                                     |                                                              |
| select idb             | 切换到指定的数据库                                           |                                                              |
| swapdb db1 db2         | 对换指定的两个数据库中的数据                                 |                                                              |

# 自动过期

| 命令                     | 作用                                            | 说明                                                         |
| ------------------------ | ----------------------------------------------- | ------------------------------------------------------------ |
| expire key seconds       | 设置 key 的生存时间，以秒为单位                 | 对一个已有生存时间的 key 执行 expire 命令，新的时间会取代旧的时间 |
| pexpire key milliseconds | 同上，但以毫秒为单位                            | 同上                                                         |
| expire key timestamp     | 设置 key 在某个时间过期，时间参数是 UNIX 时间戳 | 设置成功返回 1，key 不存在或者没法设置，返回 0               |
| pexpire key timestamp    | 同上，但是以毫秒为单位                          | 同上                                                         |
| ttl key                  | 查询 key 的生存时间，以秒为单位                 | key 不存在返回 -2，key 存在但没有设置生存时间返回 -1         |
| pttl key                 | 同上，但是以毫秒为单位                          | 同上                                                         |
| persist key              | 移除 key 的生存时间，把 key 置为永久有效        | 当 key 不存在或者没有设置生存时间返回 0                      |



# 字符串（STRING）

| 命令                                           | 作用                                                  | 说明                                                         |
| ---------------------------------------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| set key value [ex 秒数 / px 毫秒数]  [nx / xx] | 将 key 的值设置为 value                               | ex/px：指定过期时间、nx：只有键不存在才能设置、xx：只有键存在才能进行设置操作 |
| setnx key value                                | 只在键 key 不存在的情况下， 将键 key 的值设置为 value | 无法指定过期时间                                             |
| setex key seconds value                        | 将 key 的值设置为 value 并设置过期时间                |                                                              |
| psetex key milliseconds value                  | 同上，但单位为毫秒                                    |                                                              |
| get key                                        | 获取 key 的值                                         | 如果 key 不存在，返回 nil，如果 key 的值非字符串类型返回错误 |
| getset key value                               | 获取并返回旧值，设置新值                              | 同上                                                         |
| strlen key                                     | 返回 key 存储的字符串长度                             |                                                              |
| append key value                               | 把 value 追加到 key 的原值上，返回追加后值的长度      | 如果 key 不存在，就将 key 设置为 value                       |
| setrange key offset value                      | 从 offset 偏移量开始，用 value 覆写 key 存储的值      | 如果偏移量 > 字符长度，该字符自动补 0x00                     |
| getrange key start end                         | 获取字符串中 [start, end]范围的值                     | 下标从 0 开始。负数表示从末尾开始，下标从 -1 开始。          |
| incr key                                       | 对 key 的值自增                                       | 不存在的 key 当成 0，再 incr。操作范围为 64 位有符号数字     |
| incrby key increment                           | 自增一个量级                                          | 同上                                                         |
| drcr key                                       | 递减                                                  |                                                              |
| decrby key increment                           | 递减一个量级                                          |                                                              |
| mset key value [key value...]                  | 一次性设置多个键值                                    |                                                              |
| mget key [key...]                              | 获取多个 key 的值                                     | 如果某个键不存在，这个键的值以 nil 表示                      |

# 列表（LIST）

| 命令                                 | 作用                                                         | 说明                                                     |
| ------------------------------------ | ------------------------------------------------------------ | -------------------------------------------------------- |
| lpush key value [value..]            | 把一个或多个值插入到列表头部                                 | 当 key 不存在就创建列表                                  |
| lpushx key value                     | 把值插入列表头部                                             | 当 key 不存在，什么也不做                                |
| rpush key value [value...]           | 把一个或多个值插入到列表尾部                                 | 当 key 不存在就创建列表                                  |
| rpushx key value                     | 把值插入列表尾部                                             | 当 key 不存在，什么也不做                                |
| lpop key                             | 移除并返回列表 key 的头元素                                  | 当 key 不存在，返回 nil                                  |
| rpop                                 | 移除并返回列表 key 的尾元素                                  | 当 key 不存在，返回 nil                                  |
| rpoplpush source destination         | 把 source 的尾部拿出，放在 dest 的头部，并返回该元素值       |                                                          |
| lrem key count value                 | 从 key 链表中删除 value 值，删除 count 的绝对值个 value 后结束 | count>0 从表头删除，count<0 从表尾删除，count=0 移除所有 |
| llen key                             | 返回列表的长度                                               |                                                          |
| lindex key index                     | 返回列表中指定下标的元素                                     | 下标从 0 开始，负数表示从末尾开始                        |
| linsert key after/before pivot value | 将值  value 插入到列表 key 当中，位于值 pivot 之前或之后。   |                                                          |
| lset key index value                 | 将列表 key 下标为 index 的元素设置为 value                   |                                                          |
| lrange key start stop                | 返回链表中 [start,stop] 中的元素                             | lrange key 0 -1 可以查看所有元素                         |
| ltrim key start stop                 | 保留指定区间内的元素                                         |                                                          |
| blpop key [key...] timeout           | 它是 lpop 命令的阻塞版本，当给定列表内没有任何元素可供弹出的时候，连接将被 blpop 命令阻塞，直到等待超时或发现可弹出元素为止。 | 长轮询Ajax，在线聊天时，能够用到                         |
| brpop                                | 同上                                                         |                                                          |
| brpoplpush                           |                                                              |                                                          |

# 集合（SET）

| 命令                       | 作用                                            | 说明                         |
| -------------------------- | ----------------------------------------------- | ---------------------------- |
| sadd key value [value...]  | 往集合中添加元素                                | 返回被添加元素的数量         |
| sismember key value        | 判断 value 是否在集合中，是返回 1，否返回 0     |                              |
| spop key                   | 返回并删除集合中 key 中 1 个随机元素            |                              |
| srandmember key            | 返回集合 key 中，随机的 1 个元素                |                              |
| srem key value [value...]  | 删除集合中值为 value 的元素                     | 返回删除的元素的个数         |
| smove source dest value    | 将 source 集合中的元素 value 移动到 dest 集合中 | 是原子性操作                 |
| scard key                  | 返回集合中元素的个数                            |                              |
| smembers key               | 返回集中中所有的元素                            |                              |
| sinter key [key...]        | 返回集合的交集                                  |                              |
| sinterstore dest key [key] | 求出集合的交集并添加到 dest                     | 如果 dest 已存在，则将其覆盖 |
| sunion key [key...]        | 返回集合的并集                                  |                              |
| sdiff key [key...]         | 返回集合的差集                                  |                              |
| sunionstore/sdiffstore     | 类比 sinterstore                                |                              |

# 有序集合（ZSET）

| 命令                                                         | 作用                                                         | 说明                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------------------------------------------- |
| zadd key score member [score member...]                      | 将一个或多个元素及其 score 值加入到有序集合中                | 返回添加的数量                               |
| zscore key member                                            | 返回有序集合中元素的 score 值                                | 返回的 score 值以字符串形式表示              |
| zincrby key increnment member                                | 为有序集合的元素 value 的 score 加上增量 increment           | 返回新 score 值                              |
| zcard key                                                    | 返回有序集合元素的个数                                       |                                              |
| zcount key min max                                           | 返回 score 在 [min, max] 之间的元素的数量                    |                                              |
| zrange key start stop [wichscores]                           | 返回集合中下标在 [start,stop] 范围内按 score 排序后（递增）的成员 | 如果加上 wichscore 后，会连着 score 一起返回 |
| zrevrange key start stop [wichscores]                        | 返回集合中下标在 [start,stop] 范围内按 score 排序后（递减）的成员 | 同上                                         |
| zrangebyscore  key min max [withscores] [limit offset  count] | 返回集合中 score 在 [start,stop] 范围内按 score 排序后（递增）的成员 | min 和 max 可以是 -inf +inf                  |
| zrevrangebyscore                                             | 同上，但是是递减排序                                         |                                              |
| zrank key member                                             | 返回集合中 member 的排名（递增）                             |                                              |
| zrevrank                                                     | 同上，递减                                                   |                                              |
| zrem key member [member..]                                   | 移除有序集合一个或多个成员                                   |                                              |
| zremrangebyrank key start stop                               | 移除有序集合中排名在 [start,stop] 范围内的成员               |                                              |
| zremrangebyscore key min max                                 | 移除有序集合中 score 值介于 min 和 max 之间的成员            |                                              |
| zrangebylex                                                  | [详情见](http://redisdoc.com/sorted_set/zrangebylex.html)    |                                              |
| zlexcount                                                    | [详情见](http://redisdoc.com/sorted_set/zlexcount.html)      |                                              |
| zremrangebylex                                               | [详情见](http://redisdoc.com/sorted_set/zremrangebylex.html) |                                              |
| zunionstore                                                  | [详情见](http://redisdoc.com/sorted_set/zunionstore.html)    |                                              |
| zinterstore                                                  | [详情见](http://redisdoc.com/sorted_set/zinterstore.html)    |                                              |

# 哈希（HASH）

| 命令                        | 作用                                                      | 说明                                            |
| --------------------------- | --------------------------------------------------------- | ----------------------------------------------- |
| hset hashfield value        | 把 key 中 filed 域的值设为 value                          | 如果给定的哈希表并不存在， 就创建一个新的       |
| hsetnx hash field value     | 只有域 field 未存在于哈希表的情况下，把它的值设置为 value | 成功返回1，失败返回 0                           |
| hget hash field             | 返回哈希表中给定域的值。                                  | 哈希表中不存在给定域或者哈希表不存在， 返回 nil |
| hexists hash field          | 检查给定域 field 是否存在于哈希表中                       | 成功返回1，失败返回 0                           |
| hdel key field [field...]   | 删除哈希表中一个或多个指定域                              |                                                 |
| hlen key                    | 返回哈希表中域的数量                                      | 当 key 不存在返回 0                             |
| hstrlen key field           | 返回哈希表总给定域相关联的值的字符串长度                  |                                                 |
| hincrby key field increment | 为哈希表中的 field 域的值加上增量 increment               | 返回执行后 field 的值                           |
| hincrbyfloat                | 同上，增量为浮点数                                        |                                                 |
| hmset key value [key value] | 同时设置多个 域-值 到对应的哈希表中                       |                                                 |
| hmget key filed [field...]  | 返回哈希表中，一个或多个给定域的值                        |                                                 |
| hkeys key                   | 返回哈希表中的所有域                                      |                                                 |
| hvals                       | 返回哈希表中所有域的值                                    |                                                 |
| hgetall                     | 返回哈希表中所有域和值                                    |                                                 |

# 位图

| 命令                                | 作用                                                         | 说明                                |
| ----------------------------------- | ------------------------------------------------------------ | ----------------------------------- |
| setbit key offset value             | 设置 offset 对应二进制位上的值（0 或 1），返回该位上的旧值   | 空白位置填充 0，offset 最大 2^32^-1 |
| getbit key offset                   | 对 key 所储存的字符串值，获取指定偏移量上的位(bit)。         |                                     |
| bitcount key [start] [end]          | 计算给定字符串中，被设置为 1 的比特位的数量。                |                                     |
| bitpos key bit [start] [end]        | 返回位图中第一个值为 bit 的二进制位的位置                    |                                     |
| bitop operation destkey key [key …] | 对 key1，key2..keyN 作 operation，并将结果保存到 destkey 上。operation 可以是 AND、OR、NOT 、XOR |                                     |
| bitfield                            | [详情见](http://redisdoc.com/bitmap/bitfield.html)           |                                     |

# 事务

Redis 的事务中，启用的是乐观锁，只负责检测 key 没有被改动

| 命令               | 作用                                          | 说明                |
| ------------------ | --------------------------------------------- | ------------------- |
| multi              | 标记一个事务块的开始                          |                     |
| exec               | 执行所有事务块内的命令                        | 当操作被打断返回nil |
| discard            | 取消事务                                      |                     |
| watch key [key...] | 监听一个或多个 key，如果 key 有变，则事务取消 |                     |
| unwatch            | 取消 watch                                    |                     |

# 发布与订阅

| 命令                              | 作用                                             | 说明                                         |
| --------------------------------- | ------------------------------------------------ | -------------------------------------------- |
| publish channel message           | 将消息 message 发送到指定的频道 channel          | 返回接收到消息的订阅者数量                   |
| subscribe channel [channel...]    | 订阅一个或多个频道的消息                         | 返回接收到的消息                             |
| psubscribe pattern [pattern...]   | 订阅一个或多个符合给定模式的频道                 | 每个模式以 * 作为匹配符，匹配 0 个或多个字符 |
| unsubscribe channel [channel...]  | 退订指定的频道，如果没有指定，所有频道都退订     |                                              |
| punsubscribe pattern [pattern...] | 退订给定模式的频道，如果没有指定，所有频道都退订 | 每个模式以 * 作为匹配符，匹配 0 个或多个字符 |
| pubsub subcommand [argument...]   | 它由不同格式的子命令组成。见下                   |                                              |

pubsub：

- pubsub channels [pattern] 列出至少有一个订阅者的频道，订阅模式的客户端不计算在内。
- pubsub numsub [channel...] 返回给定频道的订阅者数量，订阅模式的客户端不计算在内。
- pubsub numpat 返回订阅模式的数量。

# 持久化

| 命令         | 作用                                                         | 说明                                                         |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| save         | 将 redis 实例的所有快照数据以 RDB 的形式保存到磁盘           | 会阻塞所有客户端                                             |
| bgsave       | 在后台异步保存当前数据库的数据到磁盘                         | Redis fork 出一个子进程用来保存数据，父进程继续处理客户端请求 |
| bgrewriteaof | 重写 AOF 文件，创建一个当前 AOF 文件的优化版本               | 如果 Redis 子进程正在执行快照的保存工作，这个 AOF 重写操作就会等保存工作完成之后执行 |
| lastsave     | 返回最近一次 Redis 成功将数据保存到磁盘上的时间，以 UNIX 时间戳格式表示。 |                                                              |
|              |                                                              |                                                              |



# 使用场景

- 字符串：缓存，计数，微博数，粉丝数
- 列表：微博的关注列表，粉丝列表，消息列表
- 集合：求交集并集差集，在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。Redis可以非常方便的实现如共同关注、共同粉丝、共同喜好等功能。sinterstore key1 key2 key3 。
- 有序集合：直播间的礼物榜、feed 流
- 哈希：存储用户信息，商品信息

## setbit 的实际应用

场景: 1 亿个用户, 每个用户登陆，做任意操作，记为今天活跃，否则记为不活跃

每周评出:有奖活跃用户，连续7天活动

每月评,等等...

思路: 

userId | date | active
-------|------|----
1  | 2013-07-27 | 1
1  | 2013-07-26 | 1

如果是放在表中,
1. 表急剧增大
1. 要用 group，sum运算，计算较慢

用位图法(bit-map)
1. 节约空间, 1 亿人每天的登陆情况，用 1 亿bit，约 1200WByte，约 10M 的字符就能表示
2. 计算方便

```
Log0721: '011001................0'
......
log0726 : '011001...............0
Log0727 : '0110000..............1'
    
```

1. 记录用户登陆

    每天按日期生成一个位图, 用户登陆后，把 user_id 位上的 bit 值置为 1
2. 把 1 周的位图 and 计算, 
    位上为1的，即是连续登陆的用户
```
    redis 127.0.0.1:6379> setbit mon 100000000 0
    (integer) 0
    redis 127.0.0.1:6379> setbit mon 3 1
    (integer) 0
    redis 127.0.0.1:6379> setbit mon 5 1
    (integer) 0
    redis 127.0.0.1:6379> setbit mon 7 1
    (integer) 0
    redis 127.0.0.1:6379> setbit thur 100000000 0
    (integer) 0
    redis 127.0.0.1:6379> setbit thur 3 1
    (integer) 0
    redis 127.0.0.1:6379> setbit thur 5 1
    (integer) 0
    redis 127.0.0.1:6379> setbit thur 8 1
    (integer) 0
    redis 127.0.0.1:6379> setbit wen 100000000 0
    (integer) 0
    redis 127.0.0.1:6379> setbit wen 3 1
    (integer) 0
    redis 127.0.0.1:6379> setbit wen 4 1
    (integer) 0
    redis 127.0.0.1:6379> setbit wen 6 1
    (integer) 0
    redis 127.0.0.1:6379> bitop and  res mon feb wen
    (integer) 12500001
```

## 安全队列

场景: task + bak 双链表完成安全队列
	
业务逻辑:

1. Rpoplpush task bak
2. 接收返回值，并做业务处理
3. 如果成功，rpop bak 清除任务。如不成功，下次从bak表里取任务

## 异步队列

rpush 生产消息，lpop 消费消息。当 lop 没有消息时，适当 sleep 一会在重试，或者使用 blop，在没有消息的时候，阻塞住直到消息到来。

## 延迟队列

使用 zset，时间戳作为 score，消息内容作为 key，调用 zadd 生产消息，消费者用 zrangebyscore 获取 N 秒之前的数据轮询进行处理。

## 循环列表


# 参考文章

- [Redis 命令参考](http://redisdoc.com/)