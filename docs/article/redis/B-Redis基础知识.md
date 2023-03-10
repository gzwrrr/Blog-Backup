---
title: "Redis 基础知识"
shortTitle: "B-Redis 基础知识"
description: "Redis 基础知识"
icon: ""
author: 
  name: gzw
  url: 
  email: 1627121193@qq.com
isOriginal: false
date: 2022-02-22
category: 
- "redis"
- "数据库"
- "缓存"
tag:
- "redis"
- "数据库"
- "缓存"
sticky: 1
star: false
article: true
timeline: true,
dir:
  text: "Redis 基础知识"
  icon: ""
  collapsible: true
  index: true
  comment: true
headerDepth: 3
index: true
order: 2
copy:
  triggerWords: 100
  disableCopy: false
  disableSelection: false
feed:
  title: "Redis 基础知识"
  description: "Redis 基础知识"
  author:
    name: gzw
    email: 1627121193@qq.com
---



# Redis 基础知识

[[toc]]

> redis默认有16个数据库，默认使用第0个

## 1.基础语法

| 序号 | 语法                 | 解释                               |
| ---- | -------------------- | ---------------------------------- |
| 1    | `select 0~15`        | 切换数据库                         |
| 2    | `dbsize`             | 查看当前数据库的空间大小           |
| 3    | `set 键 值`          | 储存数据                           |
| 4    | `get 键`             | 获取键对应的值                     |
| 5    | `keys *`             | 查看当前数据库中所有的键           |
| 6    | `flushdb`            | 清空当前数据库                     |
| 7    | `flushall`           | 清空所有数据库                     |
| 8    | `exists 键`          | 查看当前数据库是否存在这个键       |
| 9    | `move 键 值`         | 移除当前数据库中的指定键值对       |
| 10   | `expire 键 时间`     | 指定时间结束后过期，即删除该键值对 |
| 11   | `ttl 键`             | 查看指定键值对剩余存活时间         |
| 12   | `append 键 追加的值` | 向指定键的值后追加值               |



## 2.基本类型

### 2.1 String

- 相关的用法

| 序号 | 语法                            | 解释                                                         |
| ---- | ------------------------------- | ------------------------------------------------------------ |
| 1    | `strlen 键`                     | 查看对应值的长度                                             |
| 2    | `incr 键`                       | 让指定键的值自动加1，并显示                                  |
| 3    | `decr 键`                       | 让指定键的值自动减1，并显示                                  |
| 4    | `incrby 键 增量`                | 让指定键的值增加指定的增量                                   |
| 5    | `decrby 键 减量`                | 让指定键的值减少指定的减量                                   |
| 6    | `getrange 键 起始下标 结束下标` | 得到指定键中指定范围的值                                     |
| 7    | `setrange 键 起始下标 修改的值` | 替换指定键中起始下标及往后的值为指定修改的值                 |
| 8    | `setex 键 过期时间 值`          | setex : set with expire，存入键值对并指定过期时间            |
| 9    | `setnx 键 值`                   | setnx : set if not exist，如果不存在再添加（分布式锁中常用） |
| 10   | `mset 键1 值1 键2 值2 ...`      | 同时设置多个键值对                                           |
| 11   | `mget 键1 键2 ...`              | 同时获取多个值                                               |
| 12   | `msetnx 键1 值1 键2 值2`        | 不存在才添加，原子性操作，只有全部不存在才能成功             |
| 13   | `getset 键 新值`                | 如果存在值才获取对应的值，然后再设置新的值                   |

**实战场景：**

- **缓存**： 经典使用场景，把常用信息，字符串，图片或者视频等信息放到redis中，redis作为缓存层，mysql做持久化层，降低mysql的读写压力。
- **计数器**：redis是单线程模型，一个命令执行完才会执行下一个，同时数据可以一步落地到其他的数据源。
- **session**：常见方案spring session + redis实现session共享





### 2.2 List

注：在list中，值是可以重复的，左与前对应，右与后对应，带字母 “ l ” 的命令中的 “ l ” 含义有时是list，有时是left

| 序号 | 语法                                        | 解释                                                         |
| ---- | ------------------------------------------- | ------------------------------------------------------------ |
| 1    | `lpush list名 值`                           | 从左侧添加值（头部）                                         |
| 2    | `rpush list名 值`                           | 从右侧添加值（尾部）                                         |
| 3    | `lrange list名 起始下标 结束下标`           | 查看一定范围内list的值                                       |
| 4    | `lpop list名`                               | 移除list左侧第一个值                                         |
| 5    | `rpop list名`                               | 移除list右侧第一个值                                         |
| 6    | `lindex list名 index`                       | 获取index索引对应的值                                        |
| 7    | `llen list名`                               | 获取list的长度                                               |
| 8    | `lrem list名 数量 需要移除的值`             | 移除指定的值                                                 |
| 9    | `ltrim list名 起始下标 结束下标`            | 只保留范围内的值（删除其余的值）                             |
| 10   | `rpoplpush 被pop的list1名 被push的list2名`  | 将list1最右边值移除并添加到list2的最左边（这是一个组合命令 ） |
| 11   | `lset list名 index 新值`                    | 更新list里index索引对应的值为新值                            |
| 12   | `linsert list名 before|after 指定值 插入值` | 将插入值插入到指定值的前面/后面                              |

**使用列表的技巧：**

- lpush+lpop=Stack(栈)
- lpush+rpop=Queue（队列）
- lpush+ltrim=Capped Collection（有限集合）
- lpush+brpop=Message Queue（消息队列）

**实战场景：**

- **微博TimeLine**: 有人发布微博，用lpush加入时间轴，展示新的列表信息。
- **消息队列**



### 2.3 Set

注：set中的值是不可以重复的，也是无序的

Redis 中集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)

| 序号 | 语法                                         | 解释                                           |
| ---- | -------------------------------------------- | ---------------------------------------------- |
| 1    | `sadd set名 值`                              | 添加值                                         |
| 2    | `smembers set名`                             | 查看值                                         |
| 3    | `sismember set名 被判断的值`                 | 判断set中是否有被判断的值，有则返回，否则返回0 |
| 4    | `scard set名`                                | 获取set集合中的元素个数                        |
| 5    | `srem set名 指定值`                          | 移除set中指定的值                              |
| 6    | `srandmember set名`                          | 从set中随机取出一个值                          |
| 7    | `spop set名`                                 | 从set中随机移除一个值                          |
| 8    | `smove 被移除的set1名 被移入的set2名 指定值` | 将set1中指定的值移入到set2中                   |
| 9    | `sdiff set1名 set2名`                        | 获得两个set的差集                              |
| 10   | `sinter set1名 set2名`                       | 获得两个set的交集                              |
| 11   | `sunion set1名 set2名`                       | 获得两个set的并集                              |

**实战场景：**

- **标签**（tag），给用户添加标签，或者用户给消息添加标签，这样有同一标签或者类似标签的可以给推荐关注的事或者关注的人。
- **点赞，或点踩，收藏等**，可以放到set中实现



### 2.4 Hash

注：是一个Map集合（键值对key-value），本质和String没有太大区别，只不过hash更适合存储对象

| 序号 | 语法                               | 解释                                         |
| ---- | ---------------------------------- | -------------------------------------------- |
| 1    | `hset hash名 键 值`                | 添加键值对                                   |
| 2    | `hget hash名 指定键`               | 根据指定键得到值                             |
| 3    | `hmset hash名 键1 值1 键2 值2 ...` | 同时设置多个键值对                           |
| 4    | `hmget hash名 键1 键2 ...`         | 同时获取多个值                               |
| 5    | `hgetall hash名`                   | 获取全部的键值对                             |
| 6    | `hdel hash名 指定键`               | 删除指定键的键值对                           |
| 7    | `hlen hash名`                      | 获取hash的字段数量                           |
| 8    | `hexists hash名 指定键`            | 判断指定键对应的键值对是否存在               |
| 9    | `hkeys hash名`                     | 获取hash所有的键                             |
| 10   | `hvals hash名`                     | 获取hash所有的值                             |
| 11   | `hincrby hash名 指定键 增量`       | 将指定键的值增加对应增量（增量也可以是负的） |
| 12   | `hsetnx hash 键 值`                | 不存在则添加，否则不添加                     |

**实战场景：**

- **缓存**： 能直观，相比string更节省空间，的维护缓存信息，如用户信息，视频信息等





### 2.5 Zset

注：在set的基础上增加了一个score，zset是有序的

Redis 有序集合和集合一样也是 string 类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个 double 类型的分数。redis 正是通过分数来为集合中的成员进行从小到大的排序

| 序号 | 语法                                        | 解释                                                         |
| ---- | ------------------------------------------- | ------------------------------------------------------------ |
| 1    | `zadd zset名 score 值`                      | 添加值                                                       |
| 2    | `zadd zset名 score1 值1 score2 值2 ...`     | 同时添加多个值                                               |
| 3    | `zrangebyscore zset名 -inf +inf withscores` | 根据score的大小从小到大排序排序查询，查询结果的score范围无穷小到无穷大 |
| 4    | `zrevrange zset 0 -1`                       | 同上类似，由大到小排序查询                                   |
| 5    | `zrange zset名 起始下标结束下标`            | 查看范围内的值                                               |
| 6    | `zrem zset名 指定值`                        | 删除指定的值                                                 |
| 7    | `zcard zset名`                              | 获取元素个数                                                 |
| 8    | `zcount zset名 起始下标 结束下标`           | 统计符合范围的score对应的值的总数                            |

有序集合的成员是唯一的, 但分数(score)却可以重复。有序集合是通过两种数据结构实现：

1. **压缩列表(ziplist)**: ziplist是为了提高存储效率而设计的一种特殊编码的双向链表。它可以存储字符串或者整数，存储整数时是采用整数的二进制而不是字符串形式存储。它能在O(1)的时间复杂度下完成list两端的push和pop操作。但是因为每次操作都需要重新分配ziplist的内存，所以实际复杂度和ziplist的内存使用量相关
2. **跳跃表（zSkiplist)**: 跳跃表的性能可以保证在查找，删除，添加等操作的时候在对数期望时间内完成，这个性能是可以和平衡树来相比较的，而且在实现方面比平衡树要优雅，这是采用跳跃表的主要原因。跳跃表的复杂度是O(log(n))

**实战场景：**

- **排行榜**：有序集合经典使用场景。例如小说视频等网站需要对用户上传的小说视频做排行榜，榜单可以按照用户关注数，更新时间，字数等打分，做排行





## 3.特殊类型

**三种特殊类型**

- geospatial
- hyperloglog
- bitmaps

### 3.1 geospatial

注：底层实现原理是zset，所以能用zset的命令操作

可以推算地理位置的信息: 两地之间的距离, 方圆几里的人

| 序号 | 语法                                         | 解释                                     |
| ---- | -------------------------------------------- | ---------------------------------------- |
| 1    | `geoadd  键 值（经度 纬度 名称）`            | 添加地理位置                             |
| 2    | `geopos 键 值的名称`                         | 获取经纬度                               |
| 3    | `geodist 键 名称1 名称2 单位`                | 获取两地之间的直线距离                   |
| 4    | `georadius 键 值（经度 纬度） 半径 单位 ...` | 查询出在半径内的所有已存储的地理位置     |
| 5    | `georadiusbymember 键 名称 半径 单位 ...`    | 以地理位置为中心搜寻半径内的其他地理位置 |
| 6    | `geohash 键 名称1 名称2 ...`                 | 将地理位置转换成11位的 `geohash` 字符串  |



### 3.2 hyperloglog

注：这是一个基数统计（相同元素只记作一个）算法，相较于set的优点事hyperloglog占用的内存是固定的且较小的，2^64的不同元素只占 12 KB，且这种统计法是允许误差的

这个结构可以非常省内存的去统计各种计数，比如注册 IP 数、每日访问 IP 数、页面实时UV、在线用户数，共同好友数等

| 序号 | 语法                   | 解释             |
| ---- | ---------------------- | ---------------- |
| 1    | `pfadd 键 值1 值2 ...` | 创建集合         |
| 2    | `pfcount 键`           | 统计集合里的基数 |
| 3    | `pfmerge 键1 键2`      | 合并两个集合     |



### 3.3 bitmaps

注：位存储，位图，也是一种数据结构

 可以统计用户信息，如活跃与否、登录与否、打卡天数等

| 序号 | 语法                    | 解释     |
| ---- | ----------------------- | -------- |
| 1    | `setbit 键 当前序号 值` | 添加元素 |
| 2    | `getbit 键 序号`        | 获取值   |
| 3    | `bitcount 键`           | 统计值   |





### 3.4 Stream

Redis5.0 中还增加了一个数据结构Stream，从字面上看是流类型，但其实从功能上看，应该是Redis对消息队列（MQ，Message Queue）的完善实现，至此使用 Redis 实现消息队列就有三种方式：

1. 发布/订阅模式，但是这样不能持久化，如果遇到网络断开、Redis 宕机等，消息就会被丢弃
2. 基于 List LPUSH + BRPOP 或者 基于 Sorted-Set 的实现，支持持久化，但是不支持多播以及分组消费等
3. 使用 Stream

| 序号 | 语法                                                         | 解释                                           |
| ---- | ------------------------------------------------------------ | ---------------------------------------------- |
| 1    | `xadd key * field1 value1 [field2 value2]`                   | 添加元素                                       |
| 2    | `xrange key start end [COUNT count]`                         | 按区间返回stream中的元素                       |
| 3    | `xrevrange key end start [COUNT count]`                      | 按区间返回stream中的元素，倒序                 |
| 4    | `xread COUNT count STREAMS key [key ...] [IDLE ms]`          | 从一个或多个stream中读取消息                   |
| 5    | `xlen key`                                                   | 返回stream的长度                               |
| 6    | `xdel key id [id ...]`                                       | 删除一个或多个元素                             |
| 7    | `xtrim key MAXLEN [~] count`                                 | 删除stream中多余的元素                         |
| 8    | `xgroup CREATE key group-name id-or-$`                       | 创建一个消费者组                               |
| 9    | `xgroup SETID key group-name id-or-$`                        | 为消费者组设置上一个被读取消息ID               |
| 10   | `xreadgroup GROUP group consumer COUNT count STREAMS key [key ...] [BLOCK ms] [NOACK]` | 从一个或多个stream中读取消息，带有消费者组信息 |

此外，Stream还支持以下两个特殊的数据结构：

- `$`表示当前最大的消息ID；
- `>`表示当前最小的未处理的消息ID。

**使用场景：**

1. 可用作时通信等，大数据分析，异地数据备份等
2. 消息队列：Stream可以作为一种简单的消息队列，生产者可以使用XADD将消息发送到Stream中，而消费者可以使用XREAD命令以阻塞或非阻塞模式读取消息并对其进行处理
3. 日志存储：Stream可以用于存储和检索日志消息。例如，可以使用XADD将日志消息写入Stream中，然后使用XREAD命令检索和分析这些消息
4. 时间序列数据：由于Stream支持基于时间戳的检索，因此它可以用于存储和分析时间序列数据。例如，可以将股票市场价格写入Stream中，并使用XREAD命令检索特定时间段内的价格数据进行分析
5. 事件驱动的应用程序：Stream可以用于在事件驱动的应用程序中处理事件。例如，可以使用XADD将事件写入Stream中，而应用程序可以使用XREAD命令以非阻塞模式获取并处理这些事件

总之，Redis的Stream是一种非常灵活的数据结构，可以用于多种用途，例如消息队列、日志存储、时间序列数据和事件驱动的应用程序等







## 4.过期键的删除

:::info 过期信息

expires 字典会保存所有设置了过期时间的 key 的过期数据。其中 key 是指向键空间（Redis 集群中保存的所有键）中的某个键的指针，value 是该键的毫秒精度的 UNIX 时间戳表示的过期时间

Redis 同时使用了下面两种过期策略

:::

**惰性过期：**只有当访问一个 key 时，才会判断该 key 是否过期，过期则清理。该策略可以最大化地节省 CPU 的资源占用，但是对内存不友好。极端情况下可能出现大量的过期 key 没有被访问，从而不会清理，占用大量内存

**定期过期：**每间隔一定时间，会扫描一定数量的数据库的 expires 字典中一定数量的 key，并清除其中已经过期的 key。该策略是一个这种的方案，通过调整定时扫描的时间间隔和每次扫描的限定耗时，可以再不同情况下使得 CPU 和内存资源达到最优的平衡效果