---
title: "Redis 知识点汇总"
date: 2020-01-28T20:14:18+08:00
keywords: "redis"
comments: true
tags: ["redis"]
categories: ["sql"]
image: "https://webp.debuginn.com/202303152015654.jpg"
---

**1.Redis数据结构有哪些 ？**

```shell
String    List    Hash   Set    Sorted Set
bitmap    Geo     HyperLogLog Streams
```

**2.Redis相比memcached有哪些优势？**

1. memcached 所有的值均是简单的字符串; 
2. redis 作为其替代者，支持更为丰富的数据类型; 
3. redis 的速度比 memcached 快很多 redis 可以持久化数据。

**3.Redis是单线程，如何解决并发请求访问？**

redis 利用队列技术将并发访问变为串行访问，消除了传统数据库串行控制的开销。

**4. Reids6 淘汰策略有哪些？**

noeviction: **不删除策略**, 达到最大内存限制时, 如果需要更多内存, 直接返回错误信息。大多数写命令都会导致占用更多的内存，有极少数会例外。

- LRU算法 
  - allkeys-lru:所有key通用; 
  - 优先删除最近最少使用(less recently used ,LRU) 的 key。 
  - volatile-lru:只限于设置了expire 的部分; 
  - 优先删除最近最少使用(less recently used ,LRU) 的 key。 
- 随机淘汰 
  - allkeys-random: 所有key通用; 随机删除一部分 key。 
  - volatile-random: 只限于设置了 expire 的部分; 随机删除一部分 key。 
  - volatile-ttl: 只限于设置了expire 的部分; 优先删除剩余时间(time to live,TTL) 短的key。

**5.Redis持久化方案有哪些？**

- RDB(Redis DataBase)持久化：是指用数据集快照的方式半持久化模式，记录redis数据库的所有键值对,在某个时间点将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次持久化的文件，达到数据恢复； 
- AOF(Append-only file)持久化：是指所有的命令行记录以redis命令请求协议的格式完全持久化存储，保存为aof文件。

**RDB和AOF的优缺点 ：**

- RDB持久化:
  - 优点：RDB文件紧凑，体积小，网络传输快，适合全量复制；恢复速度比AOF快很多。当然，与AOF相比，RDB最重要的优点之一是对性能的影响相对较小。 
  - 缺点：RDB文件的致命缺点在于其数据快照的持久化方式决定了必然做不到实时持久化，而在数据越来越重要的今天，数据的大量丢失很多时候是无法接受的，因此AOF持久化成为主流。此外，RDB文件需要满足特定格式，兼容性差（如老版本的Redis不兼容新版本的RDB文件）。 
- AOF持久化: 与RDB持久化相对应； 
  - 优点在于支持秒级持久化、兼容性好； 
  - 缺点是文件大、恢复速度慢、对性能影响大。

**6.Redis内存划分**

**数据:**
作为数据库，数据是最主要的部分；
这部分占用的内存会统计在used_memory中。

**进程本身运行需要的内存:**
Redis主进程本身运行肯定需要占用内存，如代码、常量池等等；
这部分内存大约几兆，在大多数生产环境中与Redis数据占用的内存相比可以忽略。
这部分内存不是由jemalloc分配，因此不会统计在used_memory中。

**缓冲内存:**
缓冲内存包括客户端缓冲区、复制积压缓冲区、AOF缓冲区等；
其中，客户端缓冲存储客户端连接的输入输出缓冲；复制积压缓冲用于部分复制功能；
AOF缓冲区用于在进行AOF重写时，保存最近的写入命令。
在了解相应功能之前，不需要知道这些缓冲的细节；这部分内存由jemalloc分配， 因此会统计在used_memory中。

**内存碎片:**
内存碎片是Redis在分配、回收物理内存过程中产生的。
例如，如果对数据的更改频繁，而且数据之间的大小相差很大，可能导致redis释放的空间在物理内存中并没有释放，但redis又无法有效利用，这就形成了内存碎片。内存碎片不会统计在used_memory中。

**7.Redis 主从复制**

复制是高可用Redis的基础，哨兵和集群都是在复制基础上实现高可用的。
复制主要实现了数据的多机备份，以及对于读操作的负载均衡和简单的故障恢复。
缺陷：故障恢复无法自动化；写操作无法负载均衡；存储能力受到单机的限制 。

**8.Redis哨兵**

在复制的基础上，哨兵实现了自动化的故障恢复。
缺陷：写操作无法负载均衡；存储能力受到单机的限制。

**9.redis缓存被击穿处理机制**

使用mutex。简单地来说，就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db，而是先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX或者Memcache的ADD）去set一个mutex key，当操作返回成功时，再进行load db的操作并回设缓存；否则，就重试整个 get缓存方法。

**10.缓存和数据库间数据一致性问题**

分布式环境下非常容易出现缓存和数据库间的数据一致性问题，针对这一点的话，如果你的项目对缓存的要求是强一致性的，那么请不要使用缓存。
只能采取合适的策略来降低缓存和数据库间数据不一致的概率，而无法保证两者间的强一致性。合适的策略包括合适的缓存更新策略，更新数据库后要及时更新缓存、缓存失败时增加重试机制，例如MQ模式的消息队列 。

**11.缓存雪崩问题**

像解决缓存穿透一样加锁排队；

建立备份缓存，缓存A和缓存B，A设置超时时间，B不设值超时时间，先从A读缓存，A没有读B，并且更新A缓存和B缓存。

**12.Redis分布式**

redis支持主从的模式。
原则：Master会将数据同步到slave，而slave不会将数据同步到master。Slave启动时会连接master来同步数据。 这是一个典型的分布式读写分离模型。利用master来插入数据，slave提供检索服务。这样可以有效减少单个机器的并发访问数量 。

**13.读写分离模型**

通过增加Slave DB的数量，读的性能可以线性增长。为了避免Master DB的单点故障，集群一般都会采用两台Master DB做双机热备，所以整个集群的读和写的可用性都非常高。
读写分离架构的缺陷在于，不管是Master还是Slave，每个节点都必须保存完整的数据，如果在数据量很大的情况下，集群的扩展能力还是受限于单个节点的存储能力，而且对于Write-intensive类型的应用，读写分离架构并不适合。

**14.数据分片模型**

为解决读写分离模型的缺陷，可以将数据分片模型应用进来。 可以将每个节点看成，都是独立的 master，然后通过业务实现数据分片。 结合上面两种模型，可以将每个 master 设计成由一个 master 和多个slave组成的模型。

**15.Redis分布式锁实现**

先拿 setnx 来争抢锁，抢到之后，再用expire给锁加一个过期时间防止锁忘记了释放。如果在setnx之后执行expire之前进程意外crash或者要重启维护了，那会怎么样？
set指令有非常复杂的参数，这个应该是可以同时把setnx和expire合成一条指令来用的！

**16.Redis做异步队列**

一般使用list结构作为队列，rpush 生产消息，lpop 消费消息。当 lpop 没有消息的时候，要适当sleep一会再重试。
缺点：在消费者下线的情况下，生产的消息会丢失，得使用专业的消息队列如rabbitmq等。 能不能生产一次消费多次呢？使用pub/sub主题订阅者模式，可以实现1:N的消息队列。

**17.Redis中海量数据的正确操作方式**

利用SCAN系列命令（SCAN、SSCAN、HSCAN、ZSCAN）完成数据迭代。

**18.是否使用过Redis集群，集群的原理是什么？**

Redis Sentinal着眼于高可用，在master宕机时会自动将slave提升为master，继续提供服务。

Redis Cluster着眼于扩展性，在单个redis内存不足时，使用Cluster进行分片存储。

**19.Redis集群方案什么情况下会导致整个集群不可用？**

如果有A，B，C三个节点的集群,在没有复制模型的情况下,如果节点B失败了，那么整个集群就会以为缺少5501-11000这个范围的槽而不可用。

**20.说说Redis哈希槽的概念**

Redis集群没有使用一致性hash，而是引入了哈希槽的概念，Redis集群有16384个哈希槽，每个key通过CRC16校验后对16384取模来决定放置哪个槽，集群的每个节点负责一部分hash槽。

**21.Redis集群会有写操作丢失吗？为什么？**

Redis并不能保证数据的强一致性，这意味这在实际中集群在特定的条件下可能会丢失写操作。

**22.怎么测试Redis的连通性？**

使用ping命令。

**23.怎么理解Redis事务？**

事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。 事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。

**24.Redis如何做内存优化？**

尽可能使用散列表（hashes），散列表（是说散列表里面存储的数少）使用的内存非常小，所以你应该尽可能的将你的数据模型抽象到一个散列表里面。
比如你的web系统中有一个用户对象，不要为这个用户的名称，姓氏，邮箱，密码设置单独的key,而是应该把这个用户的所有信息存储到一张散列表里面。

**25.Redis回收进程如何工作的？**

一个客户端运行了新的命令，添加了新的数据。Redis检查内存使用情况，如果大于max memory的限制, 则根据设定好的策略进行回收。一个新的命令被执行，等等。所以我们不断地穿越内存限制的边界，通过不断达到边界然后不断地回收回到边界以下。如果一个命令的结果导致大量内存被使用（ 例如很大的集合的交集保存到一个新的键），不用多久内存限制就会被这个内存使用量超越。

**26.MySQL里有2000w数据，redis中只存20w的数据，如何保证redis中的数据都是热点数据？**

Redis内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略。

**27.假如Redis里面有1亿个key，其中有10w个key是以某个固定的已知的前缀开头的，如果将它们全部找出来？**

使用keys指令可以扫出指定模式的key列表。如果这个redis正在给线上的业务提供服务，那使用keys指令会有什么问题？会造成阻塞卡顿。
因为redis是单线程的。keys指令会导致线程阻塞一段时间，线上服务会停顿，直到指令执行完毕，服务才能恢复。这个时候可以使用scan指令，scan指令可以 无阻塞的提取出指定模式的key列表，但是会有一定的重复概率，在客户端做一次去重就可以了，但是整体所花费的时间会比直接用keys指令长。

**28.如果有大量的key需要设置同一时间过期，一般需要注意什么？**

如果大量的key过期时间设置的过于集中，到过期的那个时间点，redis可能会出现短暂的卡顿现象。一般需要在时间上加一个随机值，使得过期时间分散一些。

## 头条面试

**1.Redis连接时的 connect 与 pconnect 的区别**

connect：脚本结束之后连接就释放了。
pconnect：脚本结束之后连接不释放，连接保持在php-fpm进程中。

**2.Redis有哪些结构时间复杂度较高**

List

**3.Redis hash的实现**

哈希对象的编码可以是 ziplist 或者 hashtable 。
ziplist 编码的哈希对象使用压缩列表作为底层实现， 每当有新的键值对要加入到哈希对象时， 程序会先将保存了键的压缩列表节点推入到压缩列表表尾， 然后再将保存了值的压缩列表节点推入到压缩列表表尾， 因此,保存了同一键值对的两个节点总是紧挨在一起， 保存键的节点在前，保存值的节点在后；先添加到哈希对象中的键值对会被放在压缩列表的表头方向， 而后来添加到哈希对象中的键值对会被放在压缩列表的表尾方向。

**4.redis 主从同步是怎样的过程？ 见第7题**

**5.redis 的 zset 怎么实现的？**

有序集合的编码可以是 ziplist 或者 skiplist 。
ziplist 编码的有序集合对象使用压缩列表作为底层实现， 每个集合元素使用两个紧挨在一起的压缩列表节点来保存， 第一个节点保存元素的成员（member）， 而第二个元素则保存元素的分值（score）。 skiplist 编码的有序集合对象使用 zset 结构作为底层实现， 一个 zset 结构同时包含一个字典和一个跳跃表。

## 小米面试

**1.Redis数据结构有哪些**

```shell
String，List，Hash，Set，Sorted Set，bitmap，Geo，HyperLogLog ，Streams
```

**2. Redis 持久化方案有哪些？**

RDB(Redis DataBase)持久化：是指用数据集快照的方式半持久化模式，记录redis数据库的所有键值对,在某个时间点将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次持久化的文件，达到数据恢复 AOFAppend-only file。
持久化： 是指所有的命令行记录以redis命令请求协议的格式完全持久化存储)保存为aof文件。

## 360 面试

**1.redis的持久化**

RDB(Redis DataBase)持久化： 是指用数据集快照的方式半持久化模式，记录redis数据库的所有键值对,在某个时间点将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次持久化的文件，达到数据恢复AOFAppend-only file。
持久化： 是指所有的命令行记录以redis命令请求协议的格式完全持久化存储)保存为aof文件。

**2.lru算法实现**

```php
<?php
/**
 * LRU是最近最少使用页面置换算法(Least Recently Used)
 * 也就是首先淘汰最长时间未被使用的页面
 */
class LRU_Cache
{

    private $array_lru = array();
    private $max_size = 0;

    function __construct($size)
    {
        // 缓存最大存储
        $this->max_size = $size;
    }

    public function set_value($key, $value)
    {
        // 如果存在，则向队尾移动，先删除，后追加
        if (array_key_exists($key, $this->array_lru)) {
            unset($this->array_lru[$key]);
        }
        // 长度检查，超长则删除首元素
        if (count($this->array_lru) > $this->max_size) {
            array_shift($this->array_lru);
        }
        // 队尾追加元素
        $this->array_lru[$key] = $value;
    }

    public function get_value($key)
    {
        $ret_value = false;

        if (array_key_exists($key, $this->array_lru)) {
            $ret_value = $this->array_lru[$key];
            // 移动到队尾
            unset($this->array_lru[$key]);
            $this->array_lru[$key] = $ret_value;
        }

        return $ret_value;
    }

    public function vardump_cache()
    {
        var_dump($this->array_lru);
    }
}

$cache = new LRU_Cache(5);
$cache->set_value("01", "01");
$cache->set_value("02", "02");
$cache->set_value("03", "03");
$cache->set_value("04", "04");
$cache->set_value("05", "05");
$cache->vardump_cache();
$cache->set_value("06", "06");
$cache->vardump_cache();
$cache->set_value("03", "03");
$cache->vardump_cache();
$cache->set_value("07", "07");
$cache->vardump_cache();
$cache->set_value("01", "01");
$cache->vardump_cache();
```