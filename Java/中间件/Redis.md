



# NoSql

## NoSql发展史：

### 单机MySql

![image-20220112222213666](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220112222213666.png) 

90年代，网站的访问量不会太大，单个数据库完全足够。大多数是静态网页，服务器没有太大的压力。

> 数据量太大，一个服务器就放不下
>
> 数据的索引太大（数据量超过300万就应该创建索引），一个机器的内存也放不下
>
> 访问量（读写混合），一个机器也承受不了

### 缓存（Memcached）+MySql+垂直拆分（解决读的压力）

80%的网站都是在读取数据，每次都要请求数据库就会很麻烦，我们可以使用缓存来保证效率。

发展过程：优化数据结构和索引 -->文件缓存（IO）--> Memcached（缓存插件）



![image-20220112223953429](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220112223953429.png) 

### 分库分表+水平拆分+MySql集群（解决写的压力）

本质：数据库（读，写）

![image-20220112225254882](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220112225254882.png) 

### 如今

定位、音乐、热榜等数据量很大，变化很快的数据出现，MySql等关系型数据库已经不够用了

**目前的基本互联网项目架构：**

![image-20220112232803009](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220112232803009.png) 

## 为什么要用NoSql：

用户的个人信息，社交网络，地理位置，用户产生的数据，用户日志等爆发性的增长，关系型数据库无法处理。

这个时候，我们就需要使用NoSql数据库，NoSql数据库可以很好的处理这些情况

## 什么是NoSql？

NoSql = Not Only Sql （不仅仅是SQL）

关系型数据：表格、行、列（POI）

泛指非关系型数据库，传统的关系型数据库很难对付web2.0时代的需求，尤其是高并发的社区暴露出来很多难以克服的问题。

NoSql在大数据环境下发展十分迅速。

很多用户信息、社交网络、地理位置等数据类型的存储不需要一个固定的格式！并且不需要多余的操作就可以横向扩展！

## NoSql的特点

> 方便扩展（数据之间没有关系，很好扩展；解耦）
>
> 大数据量高性能（redis一秒可以写8万次、读11万次，NNoSql的缓存是记录级的，是一种细粒度的缓存，性能高）
>
> 数据类型是多样性的（不需要事先设计数据库，随取随用

## 传统`RDBMS（关系型数据库）`和`NoSql`的区别

传统的RDBMS

> 结构化组织
>
> SQL
>
> 数据和关系都储存在单独的表中
>
> 数据操作，数据定义语言
>
> 严格的一致性
>
> 基础事务

NoSql数据库

> 不仅仅是数据
>
> 没有固定的查询语言
>
> 键值对存储，列存储，文档存储，图形数据库（社交关系）
>
> 最终一致性
>
> CAP定理和BASE（异地多活）
>
> 高性能、高可用、高可扩展

## 大数据时代的3V和3高

大数据时代的3V：主要是描述问题的

> 海量 Volume
>
> 多样 Variety
>
> 实时 Velocity

大数据时代的3高：主要是对程序的要求

> 高并发
>
> 高可扩 （随时水平拆分，服务器不够用就扩展服务器）
>
> 高性能 （保证用户体验）

## Alibaba数据架构演进

参考文档：https://docs.huihoo.com/infoq/qcon-alibaba-chiness-architecture-design-practice-20111021.pdf

![image-20220113213501753](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220113213501753.png)

![image-20220113213534993](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220113213534993.png)

![image-20220113213554923](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220113213554923.png)

![image-20220113213646656](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220113213646656.png)

## 大型互联网应用的问题：

数据类型太多

数据源繁多，经常重构

数据要改造，面积大

```
问题：
• 数据架构复杂，应用需要直接依赖多种类型的数据源
• 开发人员需要熟悉各种数据源，以及访问方式
• 对开发人员能力提出很高的要求
• 网站应用组装业务模型的数据需要查询多种数据源
• 带来开发的不敏捷，大量的资源消耗在无意义的模型组装上
• 网站应用直接依赖底层数据源，模型发生变更将导致所有相关应用大
面积重构
• 例如商品模型的图片属性由数据库迁至图片银行
• 数据源改造也会导致相关应用的大面积重构
• 数据水平切分
• 跨数据源定位查找问题，实施缓存和性能优化都很困难
```

> 1、商品的基本信息
>
> ​		名称、价格、商家信息
>
> ​		关系型数据库就可以解决；MySql/Oracle（淘宝早年就去IOE了！）
>
> ​		但淘宝内部的MySql不是我们使用的MySql，他们根据自己的业务定制了自己的组件
>
> 2、商品的描述、评论（文字比较多）
>
> ​		使用文档型数据库：MongoDB
>
> 3、图片
>
> ​		使用分布式文件系统：FastDFS
>
>   - 淘宝自己的：TFS
>   - Google的：GFS
>   - Hadoop的：HDFS
>   - 阿里云的：OSS
>
> 4、商品的关键字（搜索）
>
> ​		搜索引擎 solr、elasticsearh
>
> ​		ISerach（淘宝）
>
> 5、商品热门波段信息
>
> ​		内存数据库
>
> ​		Redis、Tair、Memache
>
> 6、商品的交易信息
>
> ​		三方应用

## 解决方案

```
在网站应用集群和底层数据源之间，构建⼀层代理，统⼀数据层
	统⼀数据层的特性
		模型数据映射
			实现 业务模型 各属性 与 底层不同类型数据源的 模型数据映射
			目前支持关系数据库，iSearch，redis，mongodb
		统⼀的查询和更新API
			提供了基于业务模型的统⼀一的查询和更新的API，简化网站应用跨不同数据源的开发模式。
		性能优化策略
			字段延迟加载,按需返回设置
			基于热点缓存平台的二级缓存。
			异步并行的查询数据:异步并行加载模型中来自不同数据源的字段
			并发保护： 拒绝访问频率过高的主机IP或IP段
			过滤高危的查询：例如会导致数据库崩溃的全表扫描
```

![image-20220113214154000](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220113214154000.png)

![image-20220113214225420](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220113214225420.png)



## NoSql的分类

### KV（键值对）：

新浪：Redis

美团：Redis + Tair

阿里、百度：Redis + memecache

### 文档型数据库`bson格式（和json一样）`：

MongoDB

>  一个基于分布式文件存储的数据库，C++编写，主要用来处理大量的文档！
>
> MongoDB是一个介于关系型数据库和非关系型数据库中间的产品，是非关系型数据库中功能最丰富，最像关系型数据库的产品

ConthDB

### 列存储数据库

HBase

分布式文件系统

### 图关系型数据库

不是用来存图片的，放的是关系，比如社交网络、广告推荐

Neo4j、InfoGrid

### 四者的对比

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/20201210185248915.png" alt="redis"  /> 

# Redis

## 概述

Redis 是完全开源的，遵守 BSD 协议，是一个高性能的 key-value 数据库。

支持储存的value类型相对更多，包括string、list、set、zset和hash

这些数据都支持push/pop、add/remove 以及取交集，并集，差集等更丰富的操作，并且这些操作都是原子性的

redis支持各种方法不同的排序，数据都是缓存在内存中

redis会周期性的把数据写入磁盘或者把操作写入追加的记录文件

支持master-slave（主从）同步

## 应用场景

配合关系型是数据库做高速缓存

> 高频次、热门访问的数据，降低数据库IO
>
> 分布式架构，做session共享

多样的数据结构存储持久化数据

## 安装

1. Windows：https://www.runoob.com/redis/redis-install.html

2. Linux：

   ```
   下载redis：https://redis.io/
   
   安装gcc：yum -y install gcc
   
   解压redis：tar -zvxf redis-6.2.6.tar.gz 
   
   进入解压缩目录，使用gcc编译：[root@localhost Redis]# tar -zvxf redis-6.2.6.tar.gz 
   
   安装：[root@localhost redis-6.2.6]# make install 
   默认安装路径：/usr/local/bin/
   ```

## 文件详解

Redis安装目录`/usr/local/bin/`有如下文件：

- **redis-server：**是 Redis 服务器本身。
- **redis-sentinel：**是 Redis Sentinel 可执行文件（监控和故障转移，集群使用）。
- **redis-cli：**是与 Redis 对话的命令行界面实用程序（客户端）。
- **redis-benchmark：**用于检查 Redis 性能（性能测试工具）。
- **redis-check-aof**和**redis-check-rdb**（**redis-check-dump**在 3.0 及以下版本中）：在数据文件损坏的罕见事件中很有用（修复文件）。

## 启动Redis

### 前台启动（运行redis-server 文件）

进入安装目录`/usr/local/bin/`，执行：[root@localhost bin]# redis-server 

![image-20220115232241511](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220115232241511.png)

### 后台启动

1. 将redis解压目录中的redis.conf文件拷贝到任意目录：[root@localhost redis-6.2.6]# cp redis.conf /etc/redis.conf

2. 将文件中的 `daemonize no`改为`daemonize yes`：

   ![image-20220115233519653](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220115233519653.png)

3. 进入redis安装目录：/cd usr/local/bin/

4. 启动redis，在启动命令后面跟上修改过后的配置文件的路径：[root@localhost bin]# redis-server /etc/redis.conf 

5. 查看redis进程是否启动：![image-20220115234136520](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220115234136520.png)

使用`redis-cli`客户端连接测试：![image-20220115234249694](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220115234249694.png)

## 关闭Redis

在客户端连接中执行命令：127.0.0.1:6379> shutdown

使用kill命令杀死redis进程

关闭客户端只需 ctrl + c 退出即可

## 相关知识

> redis默认有16个数据库，类似数组下标从0开始，初始默认使用0号库。
>
> 使用命令`select [index]`来切换数据库，例如：select 15 （将数据库切换到15）
>
> 同意的密码管理，所有数据库使用同样的密码
>
> `dbsize`查看当前数据库的key数量
>
> `flushdb`清空当前数据库
>
> `flushall`清空全部数据库

### 端口6379是如何来的

6379在是手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字。MERZ长期以来被antirez（Redis的作者）及其朋友当作愚蠢的代名词

![image-20220116161759289](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220116161759289.png)

### redis的单线程+多路IO复用技术

简单理解就是：一个服务端进程可以同时处理多个套接字描述符。

- **多路**：多个客户端连接（连接就是套接字描述符）
- **复用**：使用单进程就能够实现同时处理多个客户端的连接

以上是通过增加进程和线程的数量来并发处理多个套接字，免不了上下文切换的开销，而 IO 多路复用只需要一个进程就能够处理多个套接字，从而解决了上下文切换的问题。

其发展可以分 **select->poll→epoll** 三个阶段来描述

比如调用select和poll 函数，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则阻塞直到超时。得到就绪状态后进行真正的操作可以在同一个线程里执行，也可以启动线程执行（比如使用线程池）：

## Redis的五大常用数据类型

命令参考：[Redis 命令参考 (redisfans.com)](http://doc.redisfans.com/)

### Redis 针对键（key）的操作

|     命令      |                             作用                             |
| :-----------: | :----------------------------------------------------------: |
|    keys *     |                     查看当前库的所有key                      |
|  exists key   |                       判断key是否存在                        |
|   type key    |                      查看key是什么类型                       |
|    del key    |                         删除指定key                          |
|  unlink key   | 根据value选择非阻塞删除（仅将key从keeyspace原数据中删除，真正的删除会在后续的异步操作） |
| expire key 10 |                 给key设置过期时间，为10秒钟                  |
|    ttl key    |      查看还有多少秒过期，-1表示永不过期，-2表示已经过期      |
|   select 1    |                       切换到1号数据库                        |
|    dbsize     |                     查看当前库有多少key                      |
|    flushdb    |                        清空当前数据库                        |
|   flushall    |                        清空全部数据库                        |

### Redis中对字符串（String）的操作

> String是最基本的数据类型，一个key对应一个value
>
> String是二进制安全的，意味着Redis的String可以包含任何数据，比如jpg图片、或者序列化对象
>
> String是Redis的基本数据类型，一个字符串的value可以是512MB

```
Redis中String类型的数据结构为简单的动态字符串，是可以修改的字符串，内部结构实现上类似与java的ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配。
如下图所示，内部为当前实际分配空间（capacity）一般要高于实际字符串长度（len），当实际分配空间等于或大于字符串长度就需要扩容。
当字符串长度小于 1M 时，宽容就是加倍现有的空间，字符串长度超过 1M 时，宽容就只增加 1M 空间。
注意：字符串的最大长度是 512M
```

![image-20220121222702092](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220121222702092.png)

#### set key value [EX seconds] [PX milliseconds] [NX|XX]

将字符串值 `value` 关联到 `key` 。

如果 `key` 已经持有其他值， SET就覆写旧值，无视类型。

对于某个原本带有生存时间（TTL）的键来说， 当 SET 命令成功在这个键上执行时， 这个键原有的 TTL 将被清除。

可选参数:

从 Redis 2.6.12 版本开始， SET 命令的行为可以通过一系列参数来修改：

- `EX second` ：设置键的过期时间为 `second` 秒。 `SET key value EX second` 效果等同于 `SETEX key second value` 。
- `PX millisecond` ：设置键的过期时间为 `millisecond` 毫秒。 `SET key value PX millisecond` 效果等同于 `PSETEX key millisecond value` 。
- `NX` ：只在键不存在时，才对键进行设置操作。 `SET key value NX` 效果等同于 `SETNX key value` 。
- `XX` ：只在键已经存在时，才对键进行设置操作。

#### setnx key value

将 `key` 的值设为 `value` ，当且仅当 `key` 不存在。

若给定的 `key` 已经存在，则 [SETNX](http://doc.redisfans.com/string/setnx.html#setnx) 不做任何动作。

#### get key

返回 `key` 所关联的字符串值。

如果 `key` 不存在那么返回特殊值 `nil` 。

假如 `key` 储存的值不是字符串类型，返回一个错误，因为 [GET](http://doc.redisfans.com/string/get.html#get) 只能用于处理字符串值。

#### append key value

如果 `key` 已经存在并且是一个字符串， [APPEND](http://doc.redisfans.com/string/append.html#append) 命令将 `value` 追加到 `key` 原来的值的末尾。

如果 `key` 不存在， [APPEND](http://doc.redisfans.com/string/append.html#append) 就简单地将给定 `key` 设为 `value` ，就像执行 `SET key value` 一样。

#### incr key

将 `key` 中储存的数字值增一。

如果 `key` 不存在，那么 `key` 的值会先被初始化为 `0` ，然后再执行 [INCR](http://doc.redisfans.com/string/incr.html#incr) 操作。

如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。

本操作的值限制在 64 位(bit)有符号数字表示之内。

> INCR 操作是原子性的（效果相当于线程安全）
>
> 原子性操作是指不会被线程调度机制打断的操作，这种操作一旦开始，就一直运行到结束，中间不会有任何context switch（切换到另一个线程）
>
> 在单线程中，能够在单条指令中完成的操作都可以认为是原子性的，因为中断只能发生在指令之间
>
> 在多线程中，不能被其他线程打断的操作就是原子操作
>
> Redis单命令的原子性主要得益于它的单线程

#### incrby key increment

将 `key` 所储存的值加上增量 `increment` ，`key`不存在默认为 `0 + increment`。

#### **decrby key decrement**

将 `key` 所储存的值减去减量 `decrement`，`key`不存在默认为 `0 - increment` 。

#### decr key

将 `key` 中储存的数字值减一。

其余操作与 INCR 相同

#### mset key value [key value ...]

同时设置一个或多个 `key-value` 对。如果有相同的 `key` 就会覆盖原来的值

#### mget key [key ...]

返回所有(一个或多个)给定 `key` 的值。

如果给定的 `key` 里面，有某个 `key` 不存在，那么这个 `key` 返回特殊值 `nil` 。因此，该命令永不失败。

#### msetnx key value [key value ...]

同时设置一个或多个 `key-value` 对，当且仅当所有给定 `key` 都不存在。

即使只有一个给定 `key` 已存在， MSETNX也会拒绝执行所有给定 `key` 的设置操作。

#### getrange key start end

返回 `key` 中字符串值的子字符串，字符串的截取范围由 `start` 和 `end` 两个偏移量决定(包括 `start` 和 `end` 在内)。

负数偏移量表示从字符串最后开始计数， `-1` 表示最后一个字符， `-2` 表示倒数第二个，以此类推。

GETRANGE通过保证子字符串的值域(range)不超过实际字符串的值域来处理超出范围的值域请求。

#### **setrange key offset value**

用 `value` 参数覆写(overwrite)给定 `key` 所储存的字符串值，从偏移量 `offset` 开始。

不存在的 `key` 当作空白字符串处理。

#### **setex key seconds value**

将值 `value` 关联到 `key` ，并将 `key` 的生存时间设为 `seconds` (以秒为单位)。

如果 `key` 已经存在， [SETEX](http://doc.redisfans.com/string/setex.html#setex) 命令将覆写旧值。

#### **getset key value**

将给定 `key` 的值设为 `value` ，并返回 `key` 的旧值(old value)。

当 `key` 存在但不是字符串类型时，返回一个错误。

### Redis中对列表（List）的操作

> 单键多值：name = ["yyb","xxy","pink"]
>
> Redis列表是简单的字符串列表，按照插入顺序排序，你可以添加一个元素到列表的头部或者尾部。
>
> 它的底层实际上是一个双向链表，对两端的操作性能很高，通过索引下标操作中间结点的性能可能会较差

```java
Redis中，List 的底层是 quickList（快速列表）
quickList是一个ziplist（压缩列表）组成的双向链表。每个节点使用ziplist来保存数据。
当数据量较小时，quickList列表里面就是一个ziplist
数据量较大时，就会采用ziplist组成的双向链表。

因为普通列表需要的指针空间太大，例如：
    private class Node{
        private Node prve;
        private Object data;
        private Node next;
        public Node(Node prve, Object data, Node next) {
            this.prve = prve;
            this.data = data;
            this.next = next;
        }
    }
quickList就是将多个ziplist用双指针穿起来使用，这样既满足了快速插入删除的性能，又不会出现太大的空间冗余问题。
```

#### **lpush key value [value ...]**

将一个或多个值 `value` 插入到列表 `key` 的表头（左边）

如果有多个 `value` 值，那么各个 `value` 值按从左到右的顺序依次插入到表头： 比如说，对空列表 `mylist` 执行命令 `LPUSH mylist a b c` ，列表的值将是 `c b a` ，这等同于原子性地执行 `LPUSH mylist a` 、 `LPUSH mylist b` 和 `LPUSH mylist c` 三个命令。

如果 `key` 不存在，一个空列表会被创建并执行 [LPUSH](http://doc.redisfans.com/list/lpush.html#lpush) 操作。

当 `key` 存在但不是列表类型时，返回一个错误。redis 2.4 之前的版本只支持一次插入一个值

#### **rpush key value [value ...]**

将一个或多个值 `value` 插入到列表 `key` 的表尾（右边），与 lpush相似

#### **lpop key**

移除并返回列表 `key` 的头元素。（取值，并且取一个少一个）

#### **rpop key**

移除并返回列表 `key` 的尾元素。（取值，并且取一个少一个）

#### **rpoplpush source destination**

命令 [RPOPLPUSH](http://doc.redisfans.com/list/rpoplpush.html#rpoplpush) 在一个原子时间内，执行以下两个动作：

- 将列表 `source` 中的最后一个元素(尾元素)弹出，并返回给客户端。
- 将 `source` 弹出的元素插入到列表 `destination` ，作为 `destination` 列表的的头元素。

举个例子，你有两个列表 `source` 和 `destination` ， `source` 列表有元素 `a, b, c` ， `destination` 列表有元素 `x, y, z` ，执行 `RPOPLPUSH source destination` 之后， `source` 列表包含元素 `a, b` ， `destination` 列表包含元素 `c, x, y, z` ，并且元素 `c` 会被返回给客户端。

如果 `source` 不存在，值 `nil` 被返回，并且不执行其他动作。

如果 `source` 和 `destination` 相同，则列表中的表尾元素被移动到表头，并返回该元素，可以把这种特殊情况视作列表的旋转(rotation)操作。

#### **lrange key start stop**

返回列表 `key` 中指定区间内的元素，区间以偏移量 `start` 和 `stop` 指定。

`127.0.0.1:6379> LRANGE k2 0 -1` 0表示左边第一个，-1 表示右边第一个，（0 -1 表示所有元素）

#### **lindex key index**

返回列表 `key` 中，下标为 `index` 的元素。（不会移出元素）

#### llen key

获取列表的长度

如果 `key` 不存在，则 `key` 被解释为一个空列表，返回 `0` .

如果 `key` 不是列表类型，返回一个错误。

#### **linsert key BEFORE|AFTER pivot value**

将值 `value` 插入到列表 `key` 当中，位于 `pivot` （值）之前或之后。

当 `pivot` 不存在于列表 `key` 时，不执行任何操作。

当 `key` 不存在时， `key` 被视为空列表，不执行任何操作。

如果 `key` 不是列表类型，返回一个错误。

#### **lrem key count value**

根据参数 `count` 的值，移除列表中与参数 `value` 相等的元素。

`count` 的值可以是以下几种：

- `count > 0` : 从表头开始向表尾搜索，移除与 `value` 相等的元素，数量为 `count` 。
- `count < 0` : 从表尾开始向表头搜索，移除与 `value` 相等的元素，数量为 `count` 的绝对值。
- `count = 0` : 移除表中所有与 `value` 相等的值。

#### **lset key index value**

将列表 `key` 下标为 `index` 的元素的值设置为 `value` （替换）。

当 `index` 参数超出范围，或对一个空列表( `key` 不存在)进行 [LSET](http://doc.redisfans.com/list/lset.html#lset) 时，返回一个错误。

### Redis中对集合（Set）的操作

> Redis set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以**自动排重**的，里面的元素不能重复。
>
> 并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。
>
> Redis的Set是string类型的 **无序集合**（存储没有顺序）。它底层其实是一个value为null的hash表，所以添加，删除，查找的**复杂度都是O(1)**
>
> Set集合中将元素称之为 member 

```
Set数据结构是dict字典，字典是用哈希表实现的。
Java中HashSet的内部实现使用的是HashMap，只不过所有的value都指向同一个对象。Redis的set结构也是一样，它的内部也使用hash结构，所有的value都指向同一个内部值。
```

#### sadd key value1 value2

将一个或多个 member 元素加入到集合 key 中，已经存在的 member 元素将被忽略

#### smembers key

取出该集合的所有值。

#### sismember key value

判断集合<key>是否为含有该<value>值，有1，没有0

#### scard key

返回该集合的元素个数。

#### srem  key value1 value2

删除集合中的某个元素。

#### spop key

随机从该集合中吐出一个值，取出后原集合中就不存在这个值了，全部取出后该key就会删除

#### srandmember key n

随机从该集合中取出n个值。但不会从集合中删除 。

#### smove source destination value

value把集合中一个值从一个集合移动到另一个集合

#### sinter key1 key2

返回两个集合的交集元素。

#### sunion key1 key2

返回两个集合的并集元素。

#### sdiff key1 key2

返回两个集合的**差集**元素(key1中的，不包含key2中的)

### Redis中对哈希（Hash）的操作

> Redis hash 是一个键值对集合。
>
> Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。
>
> 类似Java里面的Map<String,Object>

```
Hash类型对应的数据结构是两种：ziplist（压缩列表），hashtable（哈希表）。当field-value长度较短且个数较少时，使用ziplist，否则使用hashtable。
```

#### 例子

**用户ID为查找的key，存储的value用户对象包含姓名，年龄，生日等信息**

如果用普通的key/value结构来存储，主要有以下2种存储方式：

**第一种：将用户关键信息做为 `key`，把用户对象转换成 `json` 字符串存放在 `redis` 中**

```
user : {id=1,name=yyb,age=20,data=11.11}
```

> 缺点：每次修改需要将数据反序列化转换成对象，修改完成后再序列化转回去，开销太大

**第二种：将用户关键信息和字段名称作为 `key`，把字段的值作为 `value` 存放到 `redis` 中**

```
user:id		01
user:name	yyb
user:age	20
user:data	11.11
```

> 缺点：如果一个表中数据量和字段数都大，存储就会特别混乱。

**使用 `Hash`就可以很好的解决这个问题**

`Hash`可以使用用户的关键信息作为 `key`，把字段信息按照字段和值对应的存储进 `redis`

![image-20220122200025522](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220122200025522.png) 

#### hset key field value

给<key>集合中的 <field>键赋值<value>

#### hget key1 field

从<key1>集合<field>取出 value 

#### hmset key1 field1 value1 field2 value2

批量设置hash的值

#### hexists key1 field 

查看哈希表 key 中，给定域 field 是否存在。 

#### hkeys key

列出该hash集合的所有field

#### hvals key

列出该hash集合的所有value

#### hincrby key field increment

为哈希表 key 中的域 field 的值加上增量 1  -1

#### hsetnx key field value

将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在 .

### Redis中对有序集合（Zset）的操作

> Redis有序集合zset与普通集合set非常相似，是一个没有重复元素的字符串集合。
>
> 不同之处是有序集合的每个成员都关联了一个评分（score），这个评分（score）被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分可以是重复了 。
>
> 因为元素是有序的，所以你也可以很快的根据评分（score）或者次序（position）来获取一个范围的元素。
>
> 访问有序集合的中间元素也是非常快的，因此你能够使用有序集合作为一个没有重复成员的智能列表。

```
SortedSet(zset)是Redis提供的一个非常特别的数据结构，一方面它等价于Java的数据结构Map<String, Double>，可以给每一个元素value赋予一个权重score，另一方面它又类似于TreeSet，内部的元素会按照权重score进行排序，可以得到每个元素的名次，还可以通过score的范围来获取元素的列表。
zset底层使用了两个数据结构
（1）hash，hash的作用就是关联元素value和权重score，保障元素value的唯一性，可以通过元素value找到相应的score值。
（2）跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表。
```

#### 跳跃表

有序集合在生活中比较常见，例如根据成绩对学生排名，根据得分对玩家排名等。对于有序集合的底层实现，可以用数组、平衡树、链表等。数组不便元素的插入、删除；平衡树或红黑树虽然效率高但结构复杂；链表查询需要遍历所有效率低。Redis采用的是跳跃表。跳跃表效率堪比红黑树，实现远比红黑树简单。

**实例**

  对比有序链表和跳跃表，从链表中查询出51

（1）  有序链表

![image-20220123220857770](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220123220857770.png) 

> 要查找值为51的元素，需要从第一个元素开始依次查找、比较才能找到。共需要6次比较。

（2）  跳跃表

 ![image-20220123220903303](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220123220903303.png)

> 从第2层开始，1节点比51节点小，向后比较。
>
> 21节点比51节点小，继续向后比较，后面就是NULL了，所以从21节点向下到第1层
>
> 在第1层，41节点比51节点小，继续向后，61节点比51节点大，所以从41向下
>
> 在第0层，51节点为要查找的节点，节点被找到，共查找4次。
>
> 从此可以看出跳跃表比有序链表效率要高

---

#### zadd key score1 value1 score2 value2

将一个或多个 member 元素及其 score 值加入到有序集 key 当中。

#### zrange key start stop [WITHSCORES]

返回有序集 key 中，下标在<start><stop>之间的元素

带 `WITHSCORES`，可以让分数一起和值返回到结果集。

#### zrangebyscore key min max [withscores] [limit offset count]

返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。有序集成员按 score 值递增(从小到大)次序排列。 

#### zrevrangebyscore key max min [withscores] [limit offset count]        

同上，改为从大到小排列。 

#### zincrby key increment value

 为元素的score加上增量

#### zrem key value

删除该集合下，指定值的元素

#### zcount key min max

统计该集合，分数区间内的元素个数 

#### zrank key value

返回该值在集合中的排名，从0开始。

## Redis的配置文件

### units 单位

![image-20220124193827546](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124193827546.png) 

### includes 包含

![image-20220124194024662](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124194024662.png) 

### 网络相关配置

![image-20220124194307817](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124194307817.png) 

默认情况 `bind=127.0.0.1` 只能接受本机的访问请求

不写的情况下，无限制接受任何ip地址的访问

生产环境肯定要写你应用服务器的地址；服务器是需要远程访问的，所以需要将其注释掉

如果开启了 `protected-mode yes` 本机访问保护模式，那么在没有设定 `bind ip` 且没有设密码的情况下，Redis只允许接受本机的响应

![image-20220124200818359](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124200818359.png) 

### Port端口号

![image-20220124200949443](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124200949443.png) 

### tcp-backlog

设置tcp的backlog，backlog其实是一个连接队列，backlog队列总和 = 未完成三次握手队列 + 已经完成三次握手队列。

在高并发环境下你需要一个高backlog值来避免慢客户端连接问题。

注意Linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值（128），所以需要确认增大/proc/sys/net/core/somaxconn和/proc/sys/net/ipv4/tcp_max_syn_backlog（128）两个值来达到想要的效果

![image-20220124201318218](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124201318218.png) 

### timeout

一个空闲的客户端维持多少秒会关闭，0表示关闭该功能。即永不关闭。

![image-20220124201350997](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124201350997.png) 

### tcp-keepalive

对访问客户端的一种心跳检测，每个n秒检测一次。

单位为秒，如果设置为0，则不会进行Keepalive检测，建议设置成60 

![image-20220124201428772](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124201428772.png) 

### daemonize

是否为后台进程，设置为yes，守护进程，允许redis在后台启运行

![image-20220124201803431](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124201803431.png) 

### pidfile

存放pid文件的位置，每个实例会产生一个不同的pid文件

![image-20220124201851068](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124201851068.png) 

### loglevel 

指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为**notice**

四个级别根据使用阶段来选择，生产环境选择notice 或者warning

![image-20220124201910808](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124201910808.png) 

### logfile

设置日志文件名称

![image-20220124202041516](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124202041516.png) 

### databases 16

设定库的数量 默认16，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id

![image-20220124202139996](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124202139996.png) 

### SECURITY 安全信息，设置密码

![image-20220124202337959](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124202337959.png) 

将此注释打开，设置对应的用户名和密码

![image-20220124202626264](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124202626264.png) 

或者使用命令

访问密码的查看、设置和取消

在命令中设置密码，只是临时的。重启redis服务器，密码就还原了。

永久设置，需要再配置文件中进行设置。

![image-20220124202920194](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124202920194.png) 

### maxclients

设置redis同时可以与多少个客户端进行连接。

默认情况下为10000个客户端。

如果达到了此限制，redis则会拒绝新的连接请求，并且向这些连接请求方发出“max number of clients reached”以作回应。

![image-20220124205757768](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124205757768.png) 

### maxmemory 

建议**必须设置**，否则，将内存占满，造成服务器宕机

设置redis可以使用的内存量。一旦到达内存使用上限，redis将会试图移除内部数据，移除规则可以通过maxmemory-policy来指定。

如果redis无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，那么redis则会针对那些需要申请内存的指令返回错误信息，比如SET、LPUSH等。

但是对于无内存申请的指令，仍然会正常响应，比如GET等。如果你的redis是主redis（说明你的redis有从redis），那么在设置内存使用上限时，需要在系统中留出一些内存空间给同步队列缓存，只有在你设置的是“不移除”的情况下，才不用考虑这个因素。

![image-20220124205944063](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124205944063.png) 

### maxmemory-policy 内存不够了会怎么做

volatile-lru：使用LRU算法移除key，只对设置了过期时间的键；（最近最少使用）

allkeys-lru：在所有集合key中，使用LRU算法移除key

volatile-random：在过期集合中移除随机的key，只对设置了过期时间的键

allkeys-random：在所有集合key中，移除随机的key

volatile-ttl：移除那些TTL值最小的key，即那些最近要过期的key

noeviction：不进行移除。针对写操作，只是返回错误信息

![image-20220124210432694](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124210432694.png) 

### maxmemory-samples

设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小，redis默认会检查这么多个key并选择其中LRU的那个。

一般设置3到7的数字，数值越小样本越不准确，但性能消耗越小。

![image-20220124210507806](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220124210507806.png) 

## Redis中的发布和订阅

### 什么是发布和订阅

Redis 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息。

Redis 客户端可以订阅任意数量的频道。

#### 客户端可以订阅频道如下图

![image-20220125103152045](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220125103152045.png) 

#### 当给这个频道发布消息后，消息就会发送给订阅的客户端

![image-20220125155509131](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220125155509131.png) 

### 发布订阅命令行实现

打开一个客户端，订阅channel1

![image-20220125221928520](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220125221928520.png) 

打开另一个客户端，给channel1发布消息hello，返回值是订阅者的数量

![image-20220125222029757](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220125222029757.png) 

打开第一个客户端可以看到发送的消息

![image-20220125222126894](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220125222126894.png) 

> 注：发布的消息没有持久化，如果在订阅的客户端收不到hello，只能收到订阅后发布的消息

## Redis的新数据类型

### Bitmaps（二进制位）

现代计算机用二进制（位） 作为信息的基础单位， 1个字节等于8位， 例如“abc”字符串是由3个字节组成， 但实际在计算机存储时将其用二进制表示， “abc”分别对应的ASCII码分别是97、 98、 99， 对应的二进制分别是01100001、 01100010和01100011，如下图

![image-20220206133756623](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220206133756623.png) 

合理地使用操作位能够有效地提高内存使用率和开发效率。

Redis提供了Bitmaps这个 `数据类型` 可以实现对位的操作：

> Bitmaps本身不是一种数据类型， 实际上它就是字符串（key-value） ， 但是它可以对字符串的位进行操作。
>
>  Bitmaps单独提供了一套命令， 所以在Redis中使用Bitmaps和使用字符串的方法不太相同。 可以把Bitmaps想象成一个以位为单位的数组， 数组的每个单元只能存储0和1， 数组的下标在Bitmaps中叫做偏移量。

 ![image-20220206134114673](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220206134114673.png)

#### SETBIT key offset value

对 `key` 所储存的字符串值，设置或清除指定偏移量上的位(bit)。

位的设置或清除取决于 `value` 参数，可以是 `0` 也可以是 `1` 。

当 `key` 不存在时，自动生成一个新的字符串值。

字符串会进行伸展(grown)以确保它可以将 `value` 保存在指定的偏移量上。当字符串值进行伸展时，空白位置以 `0` 填充。

`offset` 参数必须大于或等于 `0` ，小于 2^32 (bit 映射被限制在 512 MB 之内)。

```
很多应用的用户id以一个指定数字（例如10000） 开头， 直接将用户id和Bitmaps的偏移量对应势必会造成一定的浪费， 通常的做法是每次做setbit操作时将用户id减去这个指定数字。
在第一次初始化Bitmaps时， 假如偏移量非常大， 那么整个初始化过程执行会比较慢， 可能会造成Redis的阻塞。
```

#### GETBIT key offset

对 `key` 所储存的字符串值，获取指定偏移量上的位(bit)。

当 `offset` 比字符串值的长度大，或者 `key` 不存在时，返回 `0` 。

#### bitcount

统计**字符串**被设置为1的bit数。

一般情况下，给定的整个字符串都会被进行计数，通过指定额外的 start 或 end 参数，可以让计数只在特定的位上进行。

start 和 end 参数的设置，都可以使用负数值：比如 -1 表示最后一个位，而 -2 表示倒数第二个位，start、end 是指bit组的字节的下标数，二者皆包含。

```
举例： K1 【01000001 01000000  00000000 00100001】，对应【0，1，2，3】
bitcount K1 1 2  ： 统计下标1、2字节组中bit=1的个数，即01000000  00000000
--》bitcount K1 1 2 　　--》1

bitcount K1 1 3  ： 统计下标1、2字节组中bit=1的个数，即01000000  00000000 00100001
--》bitcount K1 1 3　　--》3
```

> 注意：redis的setbit设置或清除的是bit位置，而bitcount计算的是byte位置。

#### bitop and(or/not/xor) destkey [key…]

bitop是一个复合操作， 它可以做多个Bitmaps的and（交集） 、 or（并集） 、 not（非） 、 xor（异或） 操作并将结果保存在destkey中。

![image-20220206152418322](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220206152418322.png) 

###  `Bitmaps `与 `set` 对比

假设网站有1亿用户， 每天独立访问的用户有5千万， 如果每天用集合类型和Bitmaps分别存储活跃用户可以得到表

**set和Bitmaps存储一天活跃用户对比  **

| 数据类型   | 每个用户id占用空间 | 需要存储的用户量 | 全部内存量             |
| ---------- | ------------------ | ---------------- | ---------------------- |
| 集合  类型 | 64位               | 50000000         | 64位*50000000 = 400MB  |
| Bitmaps    | 1位                | 100000000        | 1位*100000000 = 12.5MB |

很明显， 这种情况下使用Bitmaps能节省很多的内存空间， 尤其是随着时间推移节省的内存还是非常可观的

**set和Bitmaps存储独立用户空间对比**

| 数据类型 | 一天   | 一个月 | 一年  |
| -------- | ------ | ------ | ----- |
| 集合类型 | 400MB  | 12GB   | 144GB |
| Bitmaps  | 12.5MB | 375MB  | 4.5GB |

但Bitmaps并不是万金油， 假如该网站每天的独立访问用户很少， 例如只有10万（大量的僵尸用户） ， 那么两者的对比如下表所示， 很显然， 这时候使用Bitmaps就不太合适了， 因为基本上大部分位都是0。

**set和Bitmaps存储一天活跃用户对比（独立用户比较少）**

| 数据类型 | 每个userid占用空间 | 需要存储的用户量 | 全部内存量             |
| -------- | ------------------ | ---------------- | ---------------------- |
| 集合类型 | 64位               | 100000           | 64位*100000 = 800KB    |
| Bitmaps  | 1位                | 100000000        | 1位*100000000 = 12.5MB |

### HyperLogLog （基数运算）

在工作当中，我们经常会遇到与统计相关的功能需求，比如统计网站PV（PageView页面访问量）,可以使用Redis的incr、incrby轻松实现。

但像UV（UniqueVisitor，独立访客）、独立IP数、搜索记录数等需要去重和计数的问题如何解决？这种求集合中不重复元素个数的问题称为基数问题。

解决基数问题有很多种方案：

> 数据存储在MySQL表中，使用distinct count计算不重复个数
>
> 使用Redis提供的hash、set、bitmaps等数据结构来处理

以上的方案结果精确，但随着数据不断增加，导致占用空间越来越大，对于非常大的数据集是不切实际的。

**能否能够降低一定的精度来平衡存储空间？**Redis推出了HyperLogLog

`Redis` 的 `HyperLogLog` 是用来做基数统计的算法，`HyperLogLog` 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

在 `Redis` 里面，每个 `HyperLogLog` 键只需要花费 `12 KB` 内存，就可以计算接近 `2^64` 个不同元素的基数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

什么是基数?

比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, 基数(不重复元素)为5。 基数估计就是在误差可接受的范围内，快速计算基数。

#### pfadd key element [element ...] 

添加指定元素到 HyperLogLog 中，**不能添加重复元素**

将所有元素添加到指定HyperLogLog数据结构中。如果执行命令后HLL估计的近似基数发生变化，则返回1，否则返回0。

![image-20220208121554201](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220208121554201.png) 

#### pfcount key [key ...] 

计算HLL的近似基数，可以计算多个HLL，比如用HLL存储每天的UV，计算一周的UV可以使用7天的UV合并计算即可

![image-20220208122016230](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220208122016230.png)  

#### pfmerge destkey sourcekey [sourcekey ...] 

将一个或多个HLL合并后的结果存储在另一个HLL中，比如每月活跃用户可以使用每天的活跃用户来合并计算可得

![image-20220208122303713](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220208122303713.png) 

### Geospatial （地理信息，经纬度）

Redis 3.2 中增加了对GEO类型的支持。GEO，Geographic，地理信息的缩写。该类型，就是元素的2维坐标，在地图上就是经纬度。redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作。

#### geoadd key longitude latitude member  [longitude latitude member...]  

添加地理位置（经度，纬度，名称）

![image-20220208122631665](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220208122631665.png) 

> 两极无法直接添加，一般会下载城市数据，直接通过 Java 程序一次性导入。
>
> 有效的经度从 -180 度到 180 度。有效的纬度从 -85.05112878 度到 85.05112878 度。
>
> 当坐标位置超出指定范围时，该命令将会返回一个错误。
>
> 已经添加的数据，是无法再次往里面添加的。

#### geopos key member  [member...]

获得指定地区的坐标值

![image-20220208122853579](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220208122853579.png) 

#### geodist key member1 member2 [m|km|ft|mi ] 

获取两个位置之间的直线距离

**单位：**

> m 表示单位为米[默认值]。
>
> km 表示单位为千米。
>
> mi 表示单位为英里。
>
> ft 表示单位为英尺。

**如果用户没有显式地指定单位参数， 那么 GEODIST 默认使用米作为单位**

![image-20220208123114476](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220208123114476.png) 

#### georadius key longitude latitude radius m|km|ft|mi  

以给定的经纬度为中心，找出某一半径内的元素，

![image-20220208123352177](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220208123352177.png) 

## Jedis

java语言对redis的操作，相当于数据库jar包

<font color="red">**注意：使用其他机器连接需要修改配置文件中网络相关配置，并且开放防火墙的redis端口。否则会连接报错**</font>

```xml
    <!-- redis依赖 -->
    <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>4.1.0</version>
    </dependency>
```

### 连接测试

```java
import redis.clients.jedis.Jedis;

public class Jedis_demo1 {
    public static void main(String[] args) {
        /**
         * 创建Jedis对象
         * host : redis服务器ip
         * port : redis端口号
         */
        Jedis jedis = new Jedis("192.168.83.128",6379);
        /**
         * 测试
         * jedis.ping();能够连接成功会返回一个字符串:PONG
         */
        String value = jedis.ping();
        System.out.println(value);
    }
}
```

### 基本使用

redis中的命令在 jedis 中都有对应的方法

```java
    @Test
    public void demo01(){
        Jedis jedis = new Jedis("192.168.83.128",6379);
        //得到redis中的所有的key
        Set<String> keys = jedis.keys("*");
        for (String key : keys) {
            System.out.println(key);
        }

        System.out.println("------------------------------------------");
        //清空数据库
        jedis.flushAll();
        //添加数据name
        jedis.set("name","yyb");
        //获取数据name
        String name = jedis.get("name");
        System.out.println(name);

        //设置多个key和value
        jedis.mset("k1","v1","k2","v2");
        //获取多个值
        List<String> mget = jedis.mget("k1", "k2");
        for (String s : mget) {
            System.out.println(s);
        }
    }
```

### 验证码案例

```java
/**
 * 发送验证码案例
 * 六位数字的验证码，有效时间两分钟
 * 输入验证码返回成功或者失败
 * 一个手机号一天只能输入三次
 */
public class Jedis_demo2 {
    
    public static void main(String[] args) {
        Jedis_demo2 jedis_demo2 = new Jedis_demo2();
        String phoneNumber = "11111111114";

        //获取Jedis对象
        Jedis jedis = new Jedis("192.168.83.128",6379);
        //调用发送验证码的方法
        String verificationCode = jedis_demo2.getVerificationCode(phoneNumber,jedis);
        if (verificationCode == ""){
            return;
        }

        //模拟用户输入验证码
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入验证码：");
        String theVerificationCodeEnteredByTheUser = scanner.next();
        //调用验证方法
        jedis_demo2.verification(theVerificationCodeEnteredByTheUser, phoneNumber, jedis);
    }

    /**
     * 通过随机数方法生成验证码，并保存到redis
     */
    public String getVerificationCode(String phoneNumber,Jedis jedis){
        DateFormat df = new SimpleDateFormat("yyyyMMdd");
        String date = df.format(new Date());
        String verificationCode = "";
        /**
         * 记录当前用户今天发送了几次验证码
         */
        Integer integer = null;
        if (jedis.get(phoneNumber + ":" + date) != null){
            integer = Integer.valueOf(jedis.get(phoneNumber + ":" + date));
        }
        if (integer == null || integer <3){
            long incr = jedis.incr(phoneNumber + ":" + date);

        }else {
            System.out.println("您今日已经发送过三次验证码，请明天再试！！！");
            return verificationCode;
        }

        //随机数获取验证码
        for (int i = 0; i < 6; i++) {
            Random random = new Random();
            int i1 = random.nextInt(10);
            verificationCode += i1;
        }

        //模拟发送验证码
        System.out.println("您的验证码是："+verificationCode+"  30s内有效");
        //保存验证码，并设置过期时间
        jedis.setex(phoneNumber+":verificationCode",30, verificationCode.toString());
        return verificationCode;
    }

    /**
     * 从redis获取到对应的验证码，进行比对
     */
    public void verification (String verificationCode,String phoneNumber,Jedis jedis){
        Boolean flag = false;
        //判断验证码是否有效
        long ttl = jedis.ttl(phoneNumber + ":verificationCode");
        if (ttl == -2){
            System.out.println("验证码已过期");
        }else {
            //获取验证码
            String s = jedis.get(phoneNumber + ":verificationCode");
            //判断验证码是否正确
            flag = s.equals(verificationCode);
            if (flag){
                System.out.println("验证码正确");
            }else {
                System.out.println("验证码错误");
            }
        }
    }
}
```

## SpringBoot整合Redis

### 添加SpringBoot整合Redis相关依赖

```xml
<!-- redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!-- spring2.X集成redis所需common-pool2-->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

### 在 application.properties 主配置文件中进行redis配置

```properties
#Redis服务器地址
spring.redis.host=192.168.83.128
#Redis服务器连接端口
spring.redis.port=6379
#Redis数据库索引（默认为0）
spring.redis.database= 0
#连接超时时间（毫秒）
spring.redis.timeout=1800000
#连接池最大连接数（使用负值表示没有限制）
spring.redis.lettuce.pool.max-active=20
#最大阻塞等待时间(负数表示没限制)
spring.redis.lettuce.pool.max-wait=-1
#连接池中的最大空闲连接
spring.redis.lettuce.pool.max-idle=5
#连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle=0
```

### 创建Redis配置类

```java
@Configuration
@EnableCaching  //开启缓存（redis）
public class ReidsConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        template.setConnectionFactory(factory);
        //key序列化方式
        template.setKeySerializer(redisSerializer);
        //value序列化
        template.setValueSerializer(jackson2JsonRedisSerializer);
        //value hashmap序列化
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        return template;
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        //解决查询缓存转换异常的问题
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        // 配置序列化（解决乱码的问题）,过期时间600秒
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofSeconds(600))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                .disableCachingNullValues();
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                .cacheDefaults(config)
                .build();
        return cacheManager;
    }
}
```

### 编写测试方法

```java
@Controller
@RequestMapping("/redisTest")
public class RedisTestController {

    @Autowired
    private RedisTemplate redisTemplate;

    @GetMapping("redis01")
    @ResponseBody
    public String testRedis(){
        redisTemplate.opsForValue().set("name","yyb");
        String name = String.valueOf(redisTemplate.opsForValue().get("name"));
        return name;
    }
}
```

## Redis 事务

### Redis 事务的定义

Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

Redis事务的**主要作用就是串联多个命令防止别的命令插队**。

### Multi、Exec、discard

从输入 `Multi` 命令开始，输入的命令都会依次进入命令队列中，但不会执行，直到输入Exec后，Redis会将之前的命令队列中的命令依次执行。

组队的过程中可以通过discard来放弃组队，队伍中的命令不在执行，类似于关系型数据库的回滚操作。 

![image-20220209135607553](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209135607553.png) 

![image-20220209140004696](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209140004696.png) ![image-20220209140120247](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209140120247.png)

### 事务的错误处理

#### 组队时某个命令出现了报告错误，执行时整个的所有队列都会被取消。

![image-20220209140542059](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209140542059.png) ![image-20220209140753996](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209140753996.png)

#### 如果执行阶段某个命令报出了错误，则只有报错的命令不会被执行，而其他的命令都会执行，不会回滚。 

![image-20220209140632803](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209140632803.png) ![image-20220209140924464](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209140924464.png)

### 事务冲突的问题（锁）

例如：账户中只有10000块钱，同时受到三个请求。

第一个请求向给金额减8000、第二个请求想给金额减5000、第三个请求想给金额减1000

#### 悲观锁

**悲观锁(Pessimistic Lock)**， 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block（阻塞）直到它拿到锁。

**传统的关系型数据库里边就用到了很多这种锁机制**，比如**行锁**，**表锁**等，**读锁**，**写锁**等，都是在做操作之前先上锁。

![image-20220209142358098](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209142358098.png)

#### 乐观锁

**乐观锁(Optimistic Lock),** 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。**乐观锁适用于多读的应用类型，这样可以提高吞吐量**。

Redis就是利用这种check-and-set机制实现事务的。

![image-20220209142501507](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209142501507.png)

### 锁的使用

#### WATCH key [key ...]

在执行 `multi` 之前，先执行 `watch key1 [key2]` 可以监视一个(或多个) key ，如果在事务执行之前这个(或这些) key被其他命令所改动，那么事务将被打断。

![image-20220209202333830](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209202333830.png) ![image-20220209202438451](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220209202438451.png)

#### unwatch

取消 `WATCH` 命令对所有 `key` 的监视。

如果在执行 `WATCH` 命令之后，`EXEC` 命令或 `DISCARD` 命令先被执行了的话，那么就不需要再执行 `UNWATCH` 了。

因为 `EXEC` 命令会执行事务，因此 `WATCH` 命令的效果已经产生了；而 `DISCARD` 命令在取消事务的同时也会取消所有对 `key` 的监视，因此这两个命令执行之后，就没有必要执行 `UNWATCH` 了。

### 事务的三大特性

#### 单独的隔离操作 

> 事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。 

#### 没有隔离级别的概念 

> 队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行

#### 不保证原子性 

> 事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚 

### 秒杀案例

在本案例中，商品数量使用 `String` 类型记录，秒杀成功者用 `Set` 类型记录用户 `id`

#### 控制层代码

```java
@Controller
public class SeckillController {
    @Autowired
    Scekill_redis scekillRedis;

    @PostMapping("doseckill")
    @ResponseBody
    public void Spike(HttpServletResponse response, String prodid) throws IOException {
        //随机生成用户id
        String uid = new Random().nextInt(50000)+"";
        //调用业务代码
        boolean isSuccess = scekillRedis.doSeckill(uid,prodid);
        response.getWriter().print(isSuccess);
    }
}
```

#### 业务代码

```java
@Service
public class Scekill_redis {

    @Autowired
    Jedis jedis;

    public boolean doSeckill(String uid,String prodid){
        //uid 和 prodid 非空的判断
        if (uid == null || prodid == null){
            return false;
        }

        /**
         * 拼接key
         * 库存key 和 秒杀成功用户的key
         */
        String stockKey = "sk:"+prodid+":qt";
        String userKey = "sk:"+prodid+":user";

        //获取库存，如果为null，秒杀开没有开启
        String kc = jedis.get(stockKey);
        if (kc == null){
            System.out.println("秒杀还未开始");
            return false;
        }
        //判断用户是否秒杀成功过 （一人只能秒杀一次）
        if (jedis.sismember(userKey, uid)){
            System.out.println("不能重复秒杀");
            return false;
        };
        //判断库存数量

        if (Integer.parseInt(kc) <= 0){
            System.out.println("商品已经售完");
            return false;
        }

        /**
         * 秒杀过程
         * 库存 -1 ，把秒杀成功用户添加进清单
         */
        jedis.decr(stockKey);
        jedis.sadd(userKey,uid);
        System.out.println("秒杀成功");

        return true;
    }
}
```

#### 使用 ab 工具模拟并发测试

linux下安装 ab 工具：[root@localhost ~]# yum -y install httpd-tools.x86_64 

##### 测试：

```
vim postfile 模拟表单提交参数,以&符号结尾;存放当前目录。
内容：prodid=0101&
使用ab工具：
ab -n 1000 -c 100 -p /Test/postfile -T application/x-www-form-urlencoded http://192.168.0.101:8080/doseckill
-n			请求次数
-c			并发次数
-T			提交的数据类型 application/x-www-form-urlencoded
-p			用post请求，将参数放在 ~/postfile 文件中
```

使用ab工具发起请求：`ab -n 1000 -c 100 -p /Test/postfile -T application/x-www-form-urlencoded http://192.168.0.101:8080/doseckill`

##### 结果：

![image-20220211205528531](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220211205528531.png) 

![image-20220211205210301](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220211205210301.png) 

> 出现超卖问题，使用 `redis` 事务解决超卖·问题后就有可能出现以下问题。
>
> <img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220211211747855.png" alt="image-20220211211747855" style="zoom:80%;" /> 
>
> 并发量十分巨大还可能会出现连接超时问题
>
> 并发量大，库存多还有可能会出现库存遗留问题（抢的快并不一定能抢到，会出现大量的秒杀失败，但实际只有少量秒杀成功，并发结束后，还会有库存）
>
> ![image-20220213141354175](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213141354175.png) ![image-20220213141634868](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213141634868.png)

#### 解决出现的问题

##### 连接超时问题解决

使用连接池（使用工具类获取连接池，通过连接池得到 `Jedis`）

工具类代码如下

```java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

public class JedisPoolUtil {
    private static volatile JedisPool jedisPool = null;

    private JedisPoolUtil() {
    }
    
    //获取连接池
    public static JedisPool getJedisPoolInstance() {
        if (null == jedisPool) {
            synchronized (JedisPoolUtil.class) {
                if (null == jedisPool) {
                    JedisPoolConfig poolConfig = new JedisPoolConfig();
                    poolConfig.setMaxTotal(200);
                    poolConfig.setMaxIdle(32);
                    poolConfig.setMaxWaitMillis(100*1000);
                    poolConfig.setBlockWhenExhausted(true);
                    poolConfig.setTestOnBorrow(true);  // ping  PONG

                    jedisPool = new JedisPool(poolConfig, "192.168.0.102", 6379, 60000 );
                }
            }
        }
        return jedisPool;
    }

    //释放资源
    public static void release(JedisPool jedisPool, Jedis jedis) {
        if (null != jedis) {
            jedisPool.returnResource(jedis);
        }
    }
}
```

使用工具类获取 `Jedis` 如下

```java
//获取连接池
JedisPool jedisPool = JedisPoolUtil.getJedisPoolInstance();
//得到 Jedis
Jedis jedis = jedisPool.getResource();
```

##### 超卖问题解决

我们可以使用乐观锁淘汰用户解决超卖问题。

![image-20220211212042901](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220211212042901.png) 

在业务代码中使用 `watch` 监视库存，然后使用 `Redis`事务 `multi` 实现秒杀过程。具体代码如下

```java
@Service
public class Scekill_redis {
    int i = 0;

    public boolean doSeckill(String uid,String prodid){
        //获取连接池
        JedisPool jedisPool = JedisPoolUtil.getJedisPoolInstance();
        Jedis jedis = jedisPool.getResource();


        //uid 和 prodid 非空的判断
        if (uid == null || prodid == null){
            return false;
        }

        /**
         * 拼接key
         * 库存key 和 秒杀成功用户的key
         */
        String stockKey = "sk:"+prodid+":qt";
        String userKey = "sk:"+prodid+":user";

        //使用 watch 监视库存
        jedis.watch(stockKey);


        //获取库存，如果为null，秒杀开没有开启
        String kc = jedis.get(stockKey);
        if (kc == null){
            System.out.println("秒杀还未开始");
            return false;
        }
        //判断用户是否秒杀成功过 （一人只能秒杀一次）
        if (jedis.sismember(userKey, uid)){
            System.out.println("不能重复秒杀");
            return false;
        };
        //判断库存数量

        if (Integer.parseInt(kc) <= 0){
            System.out.println("商品已经售完");
            return false;
        }

        /**
         * 秒杀过程
         * 库存 -1 ，把秒杀成功用户添加进清单
         */

        /**
         * 不使用事务，会出现超卖问题
         *  jedis.decr(stockKey);
         *  jedis.sadd(userKey,uid);
        */

        //使用事务
        Transaction multi = jedis.multi();
        //组队
        multi.decr(stockKey);
        multi.sadd(userKey,uid);
        //执行
        List<Object> exec = multi.exec();

        if (exec == null || exec.size() == 0){
            System.out.println("秒杀失败");
            jedis.close();
            return false;
        }

        i++;
        System.out.println("秒杀成功:"+ i);
        return true;
    }
}
```

 ##### 解决库存遗留问题

###### `LUA` 脚本

```
Lua 是一个小巧的脚本语言，Lua脚本可以很容易的被C/C++ 代码调用，也可以反过来调用C/C++的函数，Lua并没有提供强大的库，一个完整的Lua解释器不过200k，所以Lua不适合作为开发独立应用程序的语言，而是作为嵌入式脚本语言。
很多应用程序、游戏使用LUA作为自己的嵌入式脚本语言，以此来实现可配置性、可扩展性。
这其中包括魔兽争霸地图、魔兽世界、博德之门、愤怒的小鸟等众多游戏插件或外挂。
https://www.w3cschool.cn/lua/
```

###### `LUA` 在 `Redis` 中的优势

```
将复杂的或者多步的redis操作，写为一个脚本，一次提交给redis执行，减少反复连接redis的次数。提升性能。
LUA脚本是类似redis事务，有一定的原子性，不会被其他命令插队，可以完成一些redis事务性的操作。
但是注意redis的lua脚本功能，只有在Redis 2.6以上的版本才可以使用。
利用lua脚本淘汰用户，解决超卖问题。
redis 2.6版本以后，通过lua脚本解决争抢问题，实际上是redis 利用其单线程的特性，用任务队列的方式解决多任务并发问题。
```

###### 业务层代码

```java
@Service
public class SecKill_redisByScript {
	
	static int i = 0;
	
	static String secKillScript ="local userid=KEYS[1];\r\n" + 
			"local prodid=KEYS[2];\r\n" + 
			"local qtkey='sk:'..prodid..\":qt\";\r\n" + 
			"local usersKey='sk:'..prodid..\":usr\";\r\n" +
			//执行redis命令 sismember usersKey userid (sismember sk:0101:usr 12345)
			"local userExists=redis.call(\"sismember\",usersKey,userid);\r\n" +
			//判断 userExists 是否等于 1，是就返回 2
			"if tonumber(userExists)==1 then \r\n" + 
			"   return 2;\r\n" + 
			"end\r\n" + 
			"local num= redis.call(\"get\" ,qtkey);\r\n" + 
			"if tonumber(num)<=0 then \r\n" + 
			"   return 0;\r\n" + 
			"else \r\n" + 
			"   redis.call(\"decr\",qtkey);\r\n" + 
			"   redis.call(\"sadd\",usersKey,userid);\r\n" + 
			"end\r\n" + 
			"return 1" ;

	public boolean doSecKill(String uid,String prodid) throws IOException {

		//获取Redis数据库连接池
		JedisPool jedispool =  JedisPoolUtil.getJedisPoolInstance();
		//获取jedis
		Jedis jedis=jedispool.getResource();

		//加载脚本
		String sha1=  jedis.scriptLoad(secKillScript);
		/**
		 * 执行脚本，得到脚本的返回值
		 * sha1 是已经加载的脚本名称
		 * key Count 是参数数量
		 * 后面跟上需要传入的参数，在脚本中使用 KEYS[1] 按顺序获取参数
		 */
		Object result= jedis.evalsha(sha1, 2, uid,prodid);
		//将返回值转换成 String 类型的数据
		String reString=String.valueOf(result);
		if ("0".equals( reString )  ) {
			System.err.println("已抢空！！");
		}else if("1".equals( reString )  )  {
			System.out.println("抢购成功！！！！"+"  "+ ++i);
		}else if("2".equals( reString )  )  {
			System.err.println("该用户已抢过！！");
		}else{
			System.err.println("抢购异常！！");
		}
		jedis.close();
		return true;
	}
}
```

## Redis的持久化操作

官网介绍：http://www.redis.io

Redis 提供了2个不同形式的持久化方式。

> RDB（Redis DataBase）
>
> AOF（Append Of File）

### `RDB` 操作

它是指在指定的时间间隔内将内存中的数据集快照写入磁盘， 也就是行话讲的 `Snapshot` 快照，它恢复时是将快照文件直接读到内存里。

#### 持久化是如何执行的

`Redis` 会单独创建（`fork`）一个子进程来进行持久化，会先将数据写入到 一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。 整个过程中，主进程是不进行任何 `IO` 操作的，这就确保了极高的性能 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那 `RDB` 方式要比`AOF` 方式更加的高效。**`RDB` 的缺点是最后一次持久化后的数据可能丢失**。

##### Fork

- Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等） 数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程

- 在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，Linux中引入了“**写时复制技术**”

- **一般情况父进程和子进程会共用同一段物理内存**，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。

##### `RDB`持久化流程

![image-20220213222808773](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213222808773.png) 

#### 持久化的配置

![image-20220213223956287](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213223956287.png) 

##### `dump.rdb` 文件

 `dump.rdb` 是 `RDB` 持久化文件的默认名称，可以在 `redis.conf`中配置持久化文件名称 `dump.rdb`

##### 配置位置

> rdb文件的保存路径，也可以修改。默认为 <font color="red">Redis 启动时命令行所在的目录</font>下
>
> dir "/myredis/" 

![image-20220213224235777](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213224235777.png) 

![image-20220213224432581](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213224432581.png) 

##### 如何触发RDB快照：保持策略 

配置文件中默认的快照配置

![image-20220213225612700](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213225612700.png) 

格式：save 秒钟 写操作次数

RDB 是整个内存的压缩过的 Snapshot，RDB 的数据结构，可以配置复合的快照触发条件，

```
默认是:
1分钟内改了1万次
或5分钟内改了10次
或15分钟内改了1次

禁用
不设置save指令，或者给save传入空字符串
```

![image-20220213232105511](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213232105511.png) 

##### stop-writes-on-bgsave-error

当Redis无法写入磁盘的话，直接关掉Redis的写操作。推荐yes.

![image-20220213230643348](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213230643348.png) 

##### rdbcompression 压缩文件

对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。

如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能。推荐yes.

![image-20220213230653908](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213230653908.png) 

##### rdbchecksum 检查完整性

在存储快照后，还可以让redis使用CRC64算法来进行数据校验，

但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能

推荐yes.

![image-20220213230707165](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220213230707165.png) 

 ##### 手动保存

命令 save 或 bgsave

```
save ：save时只管保存，其它不管，全部阻塞。手动保存。不建议。
bgsave：Redis 会在后台异步进行快照操作，快照同时还可以响应客户端请求。
可以通过lastsave 命令获取最后一次成功执行快照的时间
```

##### flushall

执行flushall命令，也会产生dump.rdb文件，但里面是空的，无意义

#### rdb 的备份

先通过config get dir 查询rdb文件的目录 

将 *.rdb 的文件拷贝到别的地方

#### rdb的恢复

关闭Redis

先把备份的文件拷贝到工作目录下 cp dump2.rdb dump.rdb

启动Redis, 备份数据会直接加载

#### RDB 的优势

> 适合大规模的数据恢复
>
> 对数据完整性和一致性要求不高更适合使用
>
> 节省磁盘空间
>
> 恢复速度快

![image-20220214114040105](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214114040105.png) 

#### RDB 的劣势

> Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑
>
> 虽然Redis在fork时使用了写时拷贝技术,但是如果数据庞大时还是比较消耗性能。
>
> 在备份周期在一定间隔时间做一次备份，所以如果Redis意外down掉的话，就会丢失最后一次快照之后的所有修改。

#### 如何停止 RDB

动态停止RDB：`redis-cli config set save ""` #save后给空值，表示禁用保存策略

#### 小结

![image-20220214114544964](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214114544964.png) 

### `AOF` 操作

以日志的形式来记录每个写操作（增量保存），将Redis执行过的所有写指令记录下来(读操作不记录)， 只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

#### AOF 的持久化流程

客户端的请求写命令会被append追加到AOF缓冲区内；

AOF缓冲区根据AOF持久化策略[always,everysec,no]将操作sync同步到磁盘的AOF文件中；

AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量；

Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的；

![image-20220214210237935](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214210237935.png) 

#### AOF 的默认情况

`AOF`默认不开启

![image-20220214210927829](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214210927829.png) 

可以在`redis.conf`中配置文件名称，默认为 `appendonly.aof`

![image-20220214210950090](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214210950090.png) 

`AOF`文件的保存路径，就是RDB文件的路径。

**`AOF`和`RDB`同时开启，系统默认取`AOF`的数据（数据不会存在丢失）**

开启`AOF`后，重启`redis`可以看到`aof`文件

![image-20220214211139926](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214211139926.png) 

#### AOF 启动/修复/恢复

AOF的备份机制和性能虽然和RDB不同, 但是备份和恢复的操作同RDB一样，都是拷贝备份文件，需要恢复时再拷贝到Redis工作目录下，启动系统即加载。

##### 正常恢复

> 修改默认的appendonly no，改为yes
>
> 将有数据的aof文件复制一份保存到对应目录(查看目录：config get dir)
>
> 恢复：重启redis然后重新加载

##### 异常恢复
> 修改默认的 `appendonly no`，改为 `yes`
>
> 如遇到 `AOF` 文件损坏，通过 `/usr/local/bin/redis-check-aof--fix appendonly.aof` 进行恢复
>
> ![image-20220214212630132](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214212630132.png) 
>
> 备份被写坏的 `AOF` 文件
>
> 恢复：重启 `redis`，然后重新加载

#### AOF 同步频率设置

appendfsync always

始终同步，每次Redis的写入都会立刻记入日志；性能较差但数据完整性比较好

appendfsync everysec

每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。

appendfsync no

redis不主动进行同步，把同步时机交给操作系统。

#### Rewrite 压缩（重写压缩操作）

AOF 采用文件追加方式，文件会越来越大。

为避免出现此种情况，新增了重写机制,，当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩， 只保留可以恢复数据的最小指令集。

可以使用命令bgrewriteaof

```
例如：
set k1 v1
set k2 v2
经过压缩后
set k1 v1 k2 v2
```

##### 重写原理，如何实现重写

AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，redis4.0版本后的重写，是指上就是把 rdb 的快照，以二级制的形式附在新的aof头部，作为已有的历史数据，替换掉原来的流水账操作。

**no-appendfsync-on-rewrite：**

如果 no-appendfsync-on-rewrite=yes ,不写入aof文件只写入缓存，用户请求不会阻塞，但是在这段时间如果宕机会丢失这段时间的缓存数据。（降低数据安全性，提高性能）

如果 no-appendfsync-on-rewrite=no,  还是会把数据往磁盘里刷，但是遇到重写操作，可能会发生阻塞。（数据安全，但是性能降低）

##### 触发机制，何时重写
Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发

重写虽然可以节约大量磁盘空间，减少恢复时间。但是每次重写还是有一定的负担的，因此设定Redis要满足一定条件才会进行重写。 

auto-aof-rewrite-percentage：设置重写的基准值，文件达到100%时开始重写（文件是原来重写后文件的2倍时触发）

auto-aof-rewrite-min-size：设置重写的基准值，最小文件64MB。达到这个值开始重写。

例如：文件达到70MB开始重写，降到50MB，下次什么时候开始重写？100MB

系统载入时或者上次重写完毕时，Redis会记录此时AOF大小，设为base_size,

如果Redis的AOF当前大小>= base_size +base_size*100% (默认)且当前大小>=64mb(默认)的情况下，Redis会对AOF进行重写。 

##### 重写流程

（1）bgrewriteaof触发重写，判断是否当前有bgsave或bgrewriteaof在运行，如果有，则等待该命令结束后再继续执行。

（2）主进程fork出子进程执行重写操作，保证主进程不会阻塞。

（3）子进程遍历redis内存中数据到临时文件，客户端的写请求同时写入aof_buf缓冲区和aof_rewrite_buf重写缓冲区保证原AOF文件完整以及新AOF文件生成期间的新的数据修改动作不会丢失。

（4）1).子进程写完新的AOF文件后，向主进程发信号，父进程更新统计信息。2).主进程把aof_rewrite_buf中的数据写入到新的AOF文件。

（5）使用新的AOF文件覆盖旧的AOF文件，完成AOF重写。

![image-20220214214625169](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214214625169.png) 

#### 优势

![image-20220214214707666](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214214707666.png) 

备份机制更稳健，丢失数据概率更低。

可读的日志文本，通过操作AOF稳健，可以处理误操作。

#### 劣势

比起RDB占用更多的磁盘空间。

恢复备份速度要慢。

每次读写都同步的话，有一定的性能压力。

存在个别Bug，造成恢复不能。

#### 小结

![image-20220214214804343](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220214214804343.png) 

### 总结

用哪个好？

>官方推荐两个都启用。
>
>如果对数据不敏感，可以选单独用RDB。
>
>不建议单独用 AOF，因为可能会出现Bug。
>
>如果只是做纯内存缓存，可以都不用。

### 官网建议

RDB持久化方式能够在指定的时间间隔能对你的数据进行快照存储

AOF持久化方式记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据,AOF命令以redis协议追加保存每次写的操作到文件末尾. 

Redis还能对AOF文件进行后台重写,使得AOF文件的体积不至于过大

只做缓存：如果你只希望你的数据在服务器运行的时候存在,你也可以不使用任何持久化方式

同时开启两种持久化方式

在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据, 因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整.

RDB的数据不实时，同时使用两者时服务器重启也只会找AOF文件。那要不要只使用AOF呢？ 

建议不要，因为RDB更适合用于备份数据库(AOF在不断变化不好备份)， 快速重启，而且不会有AOF可能潜在的bug，留着作为一个万一的手段。

### 性能建议

因为RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，而且只要15分钟备份一次就够了，只保留save 900 1这条规则。

如果使用AOF，好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自己的AOF文件就可以了。

```
代价：
一是带来了持续的IO
二是AOF rewrite的最后将rewrite过程中产生的新数据写到新文件造成的阻塞几乎是不可避免的。
```

只要硬盘许可，应该尽量减少AOF rewrite的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上。

默认超过原大小100%大小时重写可以改到适当的数值。

## 主从复制

主机数据更新后根据配置和策略， 自动同步到备机的 `master/slaver` 机制，`Master` 以写为主，`Slave` 以读为主

一主多从（只能有一台主服务器，从服务器一般有多个 ）

```
能够实现：
读写分离，性能扩展
容灾快速恢复
```

![image-20220215193259798](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215193259798.png) 

### 怎样使用

```
模拟三台服务器（一主两从），就是在不同的端口开启不同的redis进程，模拟三台服务器

创建 /Test/Redis 文件夹

将redis主配置文件 redis.conf 复制进去

appendonly 关掉或者换名字

开启daemonize yes，允许后台启动

修改Pid文件名字 pidfile

修改配置文件关闭 AOF

在其中创建三个配置文件
redis6379.conf	redis6380.conf	redis6381.conf

在三个配置文件中分别写入以下文件，不同的端口开启的redis，文件等都通过端口号区分
include /Test/Redis/redis.conf			include /Test/Redis/redis.conf			include /Test/Redis/redis.conf
pidfile /var/run/redis_6379.pid			pidfile /var/run/redis_6380.pid			pidfile /var/run/redis_6381.pid
port 6379								port 6380								port 6381
dbfilename dump6379.rdb					dbfilename dump6380.rdb					dbfilename dump6381.rdb
```

后台启动三个redis，目前是三个独立的服务器，没有主从关系

![image-20220215203156814](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215203156814.png) 

查看三台主机的运行情况：

通过端口号连接不同的redis `[root@localhost Redis]# redis-cli -p 6379`

查看运行情况 `info replication`

![image-20220215204002176](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215204002176.png) ![image-20220215203716492](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215203716492.png)

![image-20220215203741007](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215203741007.png) 

### 配从(库)不配主(库)

在从服务器上配置，使其成为某个主机的从服务器

命令：`slaveof ip port`

在 `6380` 和 `6381` 上配置，使其成为 `6379` 的从服务器

![image-20220215204635589](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215204635589.png) 

![image-20220215205043890](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215205043890.png) 

![image-20220215204824886](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215204824886.png) 

在主机上写，在从机上可以读取数据

主机挂掉，重启就行，一切如初

从机重启需重设：slaveof 127.0.0.1 6379

可以将配置增加到文件中。永久生效。

<font color="red">**注意：从机中不能做写操作，否则会报错**</font>

![image-20220215205259451](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20220215205259451.png) 

### 复制原理

Slave启动成功连接到master后会发送一个sync命令

Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令， 在后台进程执行完毕之后，master将传送整个数据文件（dump.rdb）到slave,以完成一次完全同步

全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。

增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步

但是只要是重新连接master,一次完全同步（全量复制）将被自动执行

![image-20220215220905015](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215220905015.png) 

### 常用的主从配置模式

#### 一主二仆

![image-20220215214731270](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215214731270.png) 

> 由于Slave是通过主机发送数据文件来同步的，所以不管什么时候连接，从机都会拥有完整的数据
>
> 主机挂掉后，从机仍然是从机，不可以进行写操作
>
> 主机重启后，如果新增记录，从机仍然可以同步更新
>
> 从机挂掉后重启也可以获取到主机的所有数据

#### 薪火相传

![image-20220215215714535](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215215714535.png) 

> 上一个Slave可以是下一个slave的Master
>
> slave同样可以接收其他 slaves的连接和同步请求
>
> 那么该slave作为了链条中下一个的master，可以有效减轻master的写压力，去中心化降低风险。
>
> 从机中途变更转向：会清除之前的数据，重新建立拷贝最新的
>
> 风险是一旦某个slave宕机，后面的slave都没法备份
>
> 主机挂了，从机还是从机，无法写数据了

#### 反客为主

薪火相传时，当一个 `master` 宕机后，后面的 `slave` 可以立刻升为 `master`，其后面的 `slave` 不用做任何修改。

用 `slaveof no one`  将从机变为主机。

![image-20220215220709169](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220215220709169.png) 

### 哨兵模式

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库。

![image-20220216204658724](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220216204658724.png) 

#### 三个 `redis` 服务器配置成一主二仆形式（主：6379）

#### 自定义的 `/myredis` 目录下新建 `sentinel.conf` 文件，名字绝不能错

```
sentinel monitor mymaster 127.0.0.1 6379 1
其中mymaster是为监控对象（主服务器）起的服务器名称
127.0.0.1 6379 是现在主服务器的端口和 ip
1 为至少有多少个哨兵同意迁移的数量。
```

#### 启动哨兵

```
[root@localhost Redis]# redis-sentinel sentinel.conf 
```

![image-20220216205735951](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220216205735951.png) 

#### 关闭 `6379` 模拟主机挂掉

当主机挂掉，从机选举中产生新的主机（大概10秒左右可以看到哨兵窗口日志，切换了新的主机）

哪个从机会被选举为主机呢？根据优先级别：`replica-priority `

原主机重启后会变为从机。

![image-20220216210829845](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220216210829845.png) 

![image-20220216211027521](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220216211027521.png) ![image-20220216211140856](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220216211140856.png)

#### 当原来挂掉的主机重启后，他会主动变成从机

![image-20220216211454282](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220216211454282.png) ![image-20220216211557221](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220216211557221.png)

#### 复制延时

由于所有的写操作都是先在 `Master`上操作，然后同步更新到 `Slave` 上，所以从 `Master` 同步到 `Slave` 机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，`Slave` 机器数量的增加也会使这个问题更加严重。

#### 故障恢复

![image-20220216212749375](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220216212749375.png) 

> 优先级在redis.conf中默认：replica-priority  100，值越小优先级越高
>
> 偏移量是指获得原主机数据最全的
>
> 每个redis实例启动后都会随机生成一个40位的runid

### 哨兵模式在java中的使用

```java
private static JedisSentinelPool jedisSentinelPool=null;

public static Jedis getJedisFromSentinel(){
    if(jedisSentinelPool==null){
        Set<String> sentinelSet=new HashSet<>();
        //添加进哨兵的 ip 和端口号
        sentinelSet.add("192.168.11.103:26379");

        JedisPoolConfig jedisPoolConfig =new JedisPoolConfig();
        jedisPoolConfig.setMaxTotal(10); //最大可用连接数
        jedisPoolConfig.setMaxIdle(5); //最大闲置连接数
        jedisPoolConfig.setMinIdle(5); //最小闲置连接数
        jedisPoolConfig.setBlockWhenExhausted(true); //连接耗尽是否等待
        jedisPoolConfig.setMaxWaitMillis(2000); //等待时间
        jedisPoolConfig.setTestOnBorrow(true); //取连接的时候进行一下测试 ping pong

        //在创建连接池实例的时候指定主服务器的名称（名称对应哨兵配置文件 sentinel.conf 中的配置）
        jedisSentinelPool=new JedisSentinelPool("mymaster",sentinelSet,jedisPoolConfig);
    	return jedisSentinelPool.getResource();
    }else{
    	return jedisSentinelPool.getResource();
    }
}
```

## Redis集群

容量不够，redis如何进行扩容？

并发写操作， redis如何分摊？

另外，主从模式，薪火相传模式，主机宕机，导致ip地址发生变化，应用程序中配置需要修改对应的主机地址、端口等信息。

之前通过代理主机来解决，但是redis3.0中提供了解决方案。就是无中心化集群配置。

### 什么是集群

Redis 集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。

Redis 集群通过分区（partition）来提供一定程度的可用性（availability）： 即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求。

### 使用方式

#### 删除 `aof` 和 `rdb` 文件

#### 制作六个实例

![image-20220217194538742](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220217194538742.png) 

#### 配置基本信息，与前面集群相同

开启 daemonize yes

Pid文件名字

指定端口

Log文件名字

dump.rdb名字

Appendonly 关掉或者换名字

#### redis cluster（集群）配置修改

```
cluster-enabled yes    打开集群模式
cluster-config-file nodes-6379.conf  设定节点配置文件名(不同的实例用不同的名称)
cluster-node-timeout 15000   设定节点失联时间，超过该时间（毫秒），集群自动进行主从切换。
```

```
include /home/bigdata/redis.conf
port 6379
pidfile "/var/run/redis_6379.pid"
dbfilename "dump6379.rdb"
dir "/home/bigdata/redis_cluster"
logfile "/home/bigdata/redis_cluster/redis_err_6379.log"
cluster-enabled yes
cluster-config-file nodes-6379.conf
cluster-node-timeout 15000
```

![image-20220217203408698](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220217203408698.png) 

#### 启动六个 redis 服务

![image-20220217203855616](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220217203855616.png) 

#### 合体

在 `redis` 的解压缩目录

```
[root@localhost src]# pwd
/usr/Redis/redis-6.2.6/src
```

执行：

```
redis-cli --cluster create --cluster-replicas 1 192.168.83.128:6379 192.168.83.128:6380 192.168.83.128:6381 192.168.83.128:6389 192.168.83.128:6390 192.168.83.128:6391
此处不要用127.0.0.1， 请用真实IP地址
redis 集群最少需要三台主机
--cluster-replicas 1  表示我们希望为集群中的每个主节点创建一个从节点。采用最简单的方式配置集群，一台主机，一台从机，六台实例，正好三组。
```

![image-20220217210313207](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220217210313207.png) 

#### 注意

redis集群最少需要三个主服务器，每个主服务器需要一个从服务器，一共需要启动六个 redis 实例，否则会报错

```
*** ERROR: Invalid configuration for cluster creation.
*** Redis Cluster requires at least 3 master nodes.
*** This is not possible with 3 nodes and 1 replicas per node.
*** At least 6 nodes are required.
```

#### 连接 redis 集群

##### 普通方式登录

可能直接进入读主机，存储数据时，会出现MOVED重定向操作。所以，应该以集群方式登录。

![image-20220217211105112](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220217211105112.png) 

##### 通过添加选项 `-c` 采用集群策略连接，设置数据会自动切换到相应的写主机

![image-20220218141439773](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220218141439773.png) 

### 通过 cluster nodes 命令查看集群信息

![image-20220217211754171](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220217211754171.png) 

### redis cluster 如何分配这六个节点?

一个集群至少要有三个主节点。

选项 `--cluster-replicas 1` 表示我们希望为集群中的每个主节点创建一个从节点。

**分配原则尽量保证每个主数据库运行在不同的IP地址，每个从库和主库不在一个 `IP` 地址上。**

### 什么是slots

执行 `redis-cli --cluster create --cluster-replicas 1` 命令搭建集群成功后，会出现下面的提示：

```shell
[OK] All 16384 slots covered.
```

`slots `就是插槽，一个 `Redis` 集群包含 `16384` 个插槽（hash slot）， 数据库中的每个键都属于这 `16384` 个插槽的其中一个， 

集群使用公式 `CRC16(key) % 16384` 来计算键 `key` 属于哪个槽， 其中 `CRC16(key)` 语句用于计算键 `key` 的 `CRC16` 校验和 。

集群中的每个节点负责处理一部分插槽。 举个例子， 如果一个集群可以有主节点， 其中：

```
节点 A 负责处理 0 号至 5460 号插槽。
节点 B 负责处理 5461 号至 10922 号插槽。
节点 C 负责处理 10923 号至 16383 号插槽。
```

在使用 `cluster nodes` 命令查看我们的集群信息时，可以在每台主机的最后看到起负责处理的插槽范围。

![image-20220217211754171](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220217211754171.png) 

通过 `redis-cli -c` 命令连接redis集群中后，写入数据时会计算 `key` 属于哪个插槽，然后会切换到该服务器，将数据写入其中：

![image-20220218142003303](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220218142003303.png) 

### 在集群中同时插入多个值

在 `redis-cli` 每次录入、查询键值，`redis` 都会计算出该 `key` 应该送往的插槽，如果不是该客户端对应服务器的插槽，`redis` 会报错，并告知应前往的 `redis` 实例地址和端口。

`redis-cli` 客户端提供了 –c 参数实现自动重定向，如 `redis-cli -c –p 6379` 登入后，再录入、查询键值对可以自动重定向。

**但是，不在一个slot下的键值，是不能使用 `mget`，`mset` 等多键操作。**

![image-20220218142733221](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220218142733221.png) 

我们 `redis` 集群中定义了一个组的概念，可以通过 `{}` 来定义组的概念，从而使 `key` 中 `{}` 内相同内容的键值对放到一个 `slot` 中去。

![image-20220218143029497](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220218143029497.png) 

### 查询集群中的值

```
192.168.83.128:6381> cluster keyslot coust		查询 coust 的插槽值
192.168.83.128:6381> cluster countkeysinslot 15495		查询 15495 插槽中有几个值（并且只能查询本主机负责处理的插槽号，超出范围返回 0）
192.168.83.128:6381> cluster getkeysinslot 15495 1		返回 1 个 15495 插槽中的值
```

![image-20220218144149707](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220218144149707.png) 

![image-20220218143858624](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220218143858624.png) 

![image-20220218143914833](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220218143914833.png) 

![image-20220218144121516](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220218144121516.png) 

### 故障恢复

如果主节点下线，从节点自动升为主节点。**注意：15秒超时**

主节点重启后，将变成新主机的从机

![image-20220218151021965](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220218151021965.png) 

如果某一段插槽的主从都挂掉，那么 `redis` 就会查看我们 `redis.conf` 中的参数 `cluster-require-full-coverage`

如果某一段插槽的主从都挂掉，而 `cluster-require-full-coverage` 为 `yes` ，那么 ，整个集群都挂掉

如果某一段插槽的主从都挂掉，而 `cluster-require-full-coverage` 为 `no`，那么，该主机负责的插槽里面数据全都不能使用，也无法存储。

### 集群的 `jedis` 开发

即使连接的不是主机，集群会自动切换主机存储。主机写，从机读。

无中心化主从集群。无论从哪台主机写的数据，其他主机上都能读到数据。

```java
import redis.clients.jedis.HostAndPort;
import redis.clients.jedis.JedisCluster;

import java.util.HashSet;
import java.util.Set;

/**
 * jedis 操作 Redis 集群
 */
public class RedisClusterDemo {
    public static void main(String[] args) {
        //创建对象
        Set<HostAndPort> set =new HashSet<HostAndPort>();
        set.add(new HostAndPort("192.168.83.128", 6379));
        set.add(new HostAndPort("192.168.83.128", 6380));
        set.add(new HostAndPort("192.168.83.128", 6381));
        set.add(new HostAndPort("192.168.83.128", 6389));
        set.add(new HostAndPort("192.168.83.128", 6390));
        set.add(new HostAndPort("192.168.83.128", 6391));
        JedisCluster jedisCluster = new JedisCluster(set);
        /**
         * JedisCluster 也可以使用单个 ip 进行连接
         * JedisCluster jedisCluster = new JedisCluster(new HostAndPort("192.168.83.128", 6379));
         */
        //进行相关的操作
        jedisCluster.set("k1","value1");
        //获取 k1 的值
        String k1 = jedisCluster.get("k1");
        System.out.println("values:"+k1);
        //关闭jedisCluster
        jedisCluster.close();
    }
}
```

### Redis 集群的好处

实现扩容

分摊压力

无中心配置相对简单

### Redis 集群的不足

多键操作是不被支持的 

多键的Redis事务是不被支持的。lua脚本不被支持

由于集群方案出现较晚，很多公司已经采用了其他的集群方案，而代理或者客户端分片的方案想要迁移至redis cluster，需要整体迁移而不是逐步过渡，复杂度较大。

 ## Redis应用问题解决

### 缓存穿透

`key` 对应的数据在数据源并不存在，每次针对此 `key` 的请求从缓存获取不到，请求都会压到数据源，从而可能压垮数据源。

比如用一个不存在的用户 `id` 获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。

> 现象：应用服务器压力变大 -> redis 命中率降低 -> 一直查询数据库
>
> 原因：redis 中查询不到数据、出现很多不正常的 `url` 访问

![image-20220221204441418](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220221204441418.png) 

#### 解决方案

一个一定不存在缓存及查询不到的数据，由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。

**对空值缓存：**如果一个查询返回的数据为空（不管是数据是否不存在），我们仍然把这个空结果（null）进行缓存，设置空结果的过期时间会很短，最长不超过五分钟

**设置可访问的名单（白名单）：**使用bitmaps类型定义一个可以访问的名单，名单id作为bitmaps的偏移量，每次访问和bitmap里面的id进行比较，如果访问id不在bitmaps里面，进行拦截，不允许访问。

**采用布隆过滤器**：(布隆过滤器（Bloom Filter）是1970年由布隆提出的。它实际上是一个很长的二进制向量(位图)和一系列随机映射函数（哈希函数）。

布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。)

将所有可能存在的数据哈希到一个足够大的bitmaps中，一个一定不存在的数据会被 这个bitmaps拦截掉，从而避免了对底层存储系统的查询压力。

**进行实时监控：**当发现Redis的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务

### 缓存击穿

`key` 对应的数据存在，但在 `redis` 中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端 `DB` 加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端 `DB` 压垮。

> 现象：数据库访问压力瞬间增大 -> redis 里面没有出现大量的 key 过期 -> redis 正常运行
>
>  原因：redis 中某个 key 过期了，目前有大量的访问需要使用这个 key 

![image-20220221205046483](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220221205046483.png) 

#### 解决方案

key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，需要考虑一个问题：缓存被“击穿”的问题。

**解决问题：**

**预先设置热门数据：**在redis高峰访问之前，把一些热门数据提前存入到redis里面，加大这些热门数据key的时长

**实时调整：**现场监控哪些数据热门，实时调整key的过期时长

**使用锁：**

 就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db。

先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX）去set一个mutex key

当操作返回成功时，再进行load db的操作，并回设缓存,最后删除mutex key；

当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法。

![image-20220221205356486](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220221205356486.png) 

### 缓存雪崩

key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。

缓存雪崩与缓存击穿的区别在于这里针对很多key缓存，前者则是某一个key

> 现象：数据库服务器压力变大崩溃
>
> 原因：在极段的时间内，查询大量 key 的集中过期情况

**正常访问**

![image-20220221210010546](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220221210010546.png) 

**缓存失效瞬间**

![image-20220221210410664](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220221210410664.png) 

#### 解决方案

缓存失效时的雪崩效应对底层系统的冲击非常可怕！

**构建多级缓存架构：**nginx缓存 + redis缓存 +其他缓存（ehcache等）

**使用锁或队列：**

用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况

**设置过期标志更新缓存：**

记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存。

**将缓存失效时间分散开：**

比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。

## 分布式锁

随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。为了解决这个问题就需要一种跨JVM的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题！

分布式锁主流的实现方案：

> 基于数据库实现分布式锁
>
>  基于缓存（Redis等）
>
>  基于Zookeeper

每一种分布式锁解决方案都有各自的优缺点：

> 性能：redis最高
>
> 可靠性：zookeeper最高

这里，我们就基于redis实现分布式锁。

### 解决方案：使用redis实现分布式锁

redis 命令：

```shell
set sku:1:info “OK” NX PX 10000

EX second ：设置键的过期时间为 second 秒。 SET key value EX second 效果等同于 SETEX key second value 。
PX millisecond ：设置键的过期时间为 millisecond 毫秒。 SET key value PX millisecond 效果等同于 PSETEX key millisecond value 。
NX ：只在键不存在时，才对键进行设置操作。 SET key value NX 效果等同于 SETNX key value 。
XX ：只在键已经存在时，才对键进行设置操作。
```

1. 多个客户端同时获取锁（setnx）

2. 获取成功，执行业务逻辑{从db获取数据，放入缓存}，执行完成释放锁（del）

3. 其他客户端等待重试

![image-20220222093313896](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220222093313896.png) 

### 代码编写

使用SpringBoot配置redis时，直接配置集群，多个服务器端口号使用逗号隔开

```properties
spring.redis.cluster.nodes=192.168.83.128:6379,192.168.83.128:6380
spring.redis.jedis.pool.max-active=10

#Redis数据库索引（默认为0）
spring.redis.database= 0
#连接超时时间（毫秒）
spring.redis.timeout=1800000
#连接池最大连接数（使用负值表示没有限制）
spring.redis.lettuce.pool.max-active=20
#最大阻塞等待时间(负数表示没限制)
spring.redis.lettuce.pool.max-wait=-1
#连接池中的最大空闲连接
spring.redis.lettuce.pool.max-idle=5
#连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle=0
```

redisTemplate 的配置与单机时的配置一样

编写代码，实现对 redis 中 num 的写入操作

```java
@GetMapping("testLock")
@ResponseBody
public String  testLock(){
    //1获取锁，setnx
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");
    //2获取锁成功、查询num的值
    if(lock){
        Object value = redisTemplate.opsForValue().get("num");
        //2.1判断num为空return
        if(StringUtils.isEmpty(value)){
            return "num不存在";
        }
        //2.2有值就转成成int
        int num = Integer.parseInt(value+"");
        //2.3把redis的num加1
        redisTemplate.opsForValue().set("num", ++num);
        //2.4释放锁，del
        redisTemplate.delete("lock");
        System.out.println(num);
        return "请求成功";
    }else{
        //3获取锁失败、每隔0.1秒再获取
        try {
            Thread.sleep(100);
            testLock();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    return "";
}
```

问题：setnx刚好获取到锁，业务逻辑出现异常，导致锁无法释放

解决：设置过期时间，自动释放锁。

### 优化之设置锁的过期时间

设置过期时间有两种方式：

1. 首先想到通过expire设置过期时间（缺乏原子性：如果在setnx和expire之间出现异常，锁也无法释放）

2. 在set时指定过期时间（推荐）

![image-20220223151042192](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223151042192.png) 

```java
//1获取锁，setnx，同时添加过期时间
Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111",3, TimeUnit.SECONDS);
```

问题：可能会释放其他服务器的锁。

场景：如果业务逻辑的执行时间是7s。执行流程如下

1. index1业务逻辑没执行完，3秒后锁被自动释放。

2. index2获取到锁，执行业务逻辑，3秒后锁被自动释放。

3. index3获取到锁，执行业务逻辑

4. index1业务逻辑执行完成，开始调用del释放锁，这时释放的是index3的锁，导致index3的业务只执行1s就被别人释放。

最终等于没锁的情况。

解决：setnx获取锁时，设置一个指定的唯一值（例如：uuid）；释放前获取这个值，判断是否自己的锁

### 优化之UUID防误删

![image-20220223151700692](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223151700692.png) 

```java
@GetMapping("testLock")
@ResponseBody
public String  testLock(){
    String uuid = UUID.randomUUID().toString();
    //1获取锁，setnx，设置值为 uuid 表示是自己的做
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", uuid,3, TimeUnit.SECONDS);
    //2获取锁成功、查询num的值
    if(lock){
        Object value = redisTemplate.opsForValue().get("num");
        //2.1判断num为空return
        if(StringUtils.isEmpty(value)){
            return "num不存在";
        }
        //2.2有值就转成成int
        int num = Integer.parseInt(value+"");
        //2.3把redis的num加1
        redisTemplate.opsForValue().set("num", ++num);
        //2.4 先判断是否是自己的锁，再释放，del
        if (uuid.equals(String.valueOf(redisTemplate.opsForValue().get("lock")))){
            redisTemplate.delete("lock");
        }
        System.out.println(num);
        return "请求成功";
    }else{
        //3获取锁失败、每隔0.1秒再获取
        try {
            Thread.sleep(100);
            testLock();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    return "";
}
```

问题：删除操作缺乏原子性。

场景：

1. index1执行删除时，查询到的lock值确实和uuid相等

   ```java
   if (uuid.equals(String.valueOf(redisTemplate.opsForValue().get("lock"))))
   ```

2. index1执行删除前，lock刚好过期时间已到，被redis自动释放

   在redis中没有了lock，没有了锁

3. index2获取了lock

   index2线程获取到了cpu的资源，开始执行方法

4. index1执行删除，此时会把index2的lock删除

   index1 因为已经在方法中了，所以不需要重新上锁。index1有执行的权限。index1已经比较完成了，这个时候，开始执行,删除的是 index2 的锁！

   ```java
   redisTemplate.delete("lock");
   ```

### 优化之LUA脚本保证删除的原子性

```java
@GetMapping("testLockLua")
public void testLockLua() {
    //1 声明一个uuid ,将做为一个value 放入我们的key所对应的值中
    String uuid = UUID.randomUUID().toString();
    //2 定义一个锁：lua 脚本可以使用同一把锁，来实现删除！
    String skuId = "25"; // 访问 skuId 为25号的商品 100008348542
    String locKey = "lock:" + skuId; // 锁住的是每个商品的数据

    // 3 获取锁
    Boolean lock = redisTemplate.opsForValue().setIfAbsent(locKey, uuid, 3, TimeUnit.SECONDS);

    // 第一种： lock 与过期时间中间不写任何的代码。
    // redisTemplate.expire("lock",10, TimeUnit.SECONDS);//设置过期时间
    // 如果true
    if (lock) {
        // 执行的业务逻辑开始
        // 获取缓存中的num 数据
        Object value = redisTemplate.opsForValue().get("num");
        // 如果是空直接返回
        if (StringUtils.isEmpty(value)) {
            return;
        }
        // 不是空 如果说在这出现了异常！ 那么delete 就删除失败！ 也就是说锁永远存在！
        int num = Integer.parseInt(value + "");
        // 使num 每次+1 放入缓存
        redisTemplate.opsForValue().set("num", String.valueOf(++num));

        /**
         * 使用lua脚本来锁
         * 定义lua 脚本
         */       
        String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
        // 使用redis执行lua执行
        DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
        redisScript.setScriptText(script);
        /**
         * 置一下返回值类型 为Long
         * 因为删除判断的时候，返回的0,给其封装为数据类型。如果不封装那么默认返回String 类型，
         * 那么返回字符串与0 会有发生错误。
         */
        redisScript.setResultType(Long.class);
        // 第一个要是script 脚本 ，第二个需要判断的key，第三个就是key所对应的值。
        redisTemplate.execute(redisScript, Arrays.asList(locKey), uuid);

    } else {
        // 其他线程等待
        try {
            // 睡眠
            Thread.sleep(1000);
            // 睡醒了之后，调用方法。
            testLockLua();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```

#### 项目中正确使用：

定义key，key应该是为每个sku定义的，也就是每个sku有一把锁。

```java
String locKey ="lock:"+skuId; // 锁住的是每个商品的数据
Boolean lock = redisTemplate.opsForValue().setIfAbsent(locKey, uuid,3,TimeUnit.SECONDS);
```

![image-20220223155026347](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223155026347.png) 

#### Lua 脚本详解：

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223153737948.png" alt="image-20220223153737948"  /> 

### 总结

1、加锁

```java
// 1. 从redis中获取锁,set k1 v1 px 20000 nx
String uuid = UUID.randomUUID().toString();
Boolean lock = this.redisTemplate.opsForValue().setIfAbsent("lock", uuid, 2, TimeUnit.SECONDS);
```

2、使用lua释放锁

```java
/**
 * 使用lua脚本来锁
 * 定义lua 脚本
 */       
String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
// 使用redis执行lua执行
DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
redisScript.setScriptText(script);
/**
 * 置一下返回值类型 为Long
 * 因为删除判断的时候，返回的0,给其封装为数据类型。如果不封装那么默认返回String 类型，
 * 那么返回字符串与0 会有发生错误。
 */
redisScript.setResultType(Long.class);
// 第一个要是script 脚本 ，第二个需要判断的key，第三个就是key所对应的值。
redisTemplate.execute(redisScript, Arrays.asList(locKey), uuid);
```

3、重试

```java
// 其他线程等待
try {
    // 睡眠
    Thread.sleep(1000);
    // 睡醒了之后，调用方法。
	testLockLua();
} catch (InterruptedException e) {
	e.printStackTrace();
}
```

为了确保分布式锁可用，我们至少要确保锁的实现同时**满足以下四个条件**：

- 互斥性。在任意时刻，只有一个客户端能持有锁。

- 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。

- 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。

- 加锁和解锁必须具有原子性。

## Redis6.0新功能

### ACL 访问控制列表

Redis ACL是Access Control List（访问控制列表）的缩写，该功能允许根据可以执行的命令和可以访问的键来限制某些连接。

在Redis 5版本之前，Redis 安全规则只有密码控制 还有通过rename 来调整高危命令比如 flushdb ， KEYS* ， shutdown 等。Redis 6 则提供ACL的功能对用户进行更细粒度的权限控制 ：

- 接入权限:用户名和密码 
- 可以执行的命令 
- 可以操作的 KEY

参考官网：https://redis.io/topics/acl

#### 命令

##### 使用acl list命令展现用户权限列表

数据说明：

![image-20220223194150439](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223194150439.png) 

##### 使用acl cat命令

查看添加权限指令类别：acl cat

![image-20220223194428786](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223194428786.png) 

加参数类型名可以查看类型下具体命令：acl cat string

![image-20220223194517221](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223194517221.png) 

##### 使用acl whoami命令查看当前用户

![image-20220223194630685](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223194630685.png) 

##### 使用acl setuser命令创建和编辑用户ACL

![image-20220223194858994](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223194858994.png) 

##### 通过命令创建新用户默认权限

acl setuser user1

![image-20220223195010734](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223195010734.png) 

在上面的示例中，我根本没有指定任何规则。如果用户不存在，这将使用just created的默认属性来创建用户。如果用户已经存在，则上面的命令将不执行任何操作。

##### 设置用户名、密码、ACL权限、并启用的用户

acl setuser user2 on >password ~cached:* +get

![image-20220223195420696](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223195420696.png) 

##### 切换用户

auth 用户名 密码

![image-20220223195327621](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223195327621.png) 

### IO多线程

Redis6终于支撑多线程了，告别单线程了吗？
IO多线程其实指客户端交互部分的网络IO交互处理模块多线程，而非执行命令多线程。Redis6执行命令依然是单线程。

#### 原理架构

Redis 6 加入多线程,但跟 Memcached 这种从 IO处理到数据访问多线程的实现模式有些差异。

Redis 的多线程部分只是用来处理网络数据的读写和协议解析，执行命令仍然是单线程。

之所以这么设计是不想因为多线程而变得复杂，需要去控制 key、lua、事务，LPUSH/LPOP 等等的并发问题。

整体的设计大体如下:

![image-20220223195746925](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223195746925.png)  

另外，多线程IO默认也是不开启的，需要再配置文件中配置

```
io-threads-do-reads yes 
io-threads 4
```

### 工具支持 Cluster

之前老版Redis想要搭集群需要单独安装ruby环境，Redis 5 将 redis-trib.rb 的功能集成到 redis-cli 。另外官方 redis-benchmark 工具开始支持 cluster 模式了，通过多线程的方式对多个分片进行压测。

![image-20220223200649008](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220223200649008.png) 

### Redis新功能持续关注

Redis6新功能还有：

1、RESP3新的 Redis 通信协议：优化服务端与客户端之间通信

2、Client side caching客户端缓存：基于 RESP3 协议实现的客户端缓存功能。为了进一步提升缓存的性能，将客户端经常访问的数据cache到客户端。减少TCP网络交互。

3、Proxy集群代理模式：Proxy 功能，让 Cluster 拥有像单实例一样的接入方式，降低大家使用cluster的门槛。不过需要注意的是代理不改变 Cluster 的功能限制，不支持的命令还是不会支持，比如跨 slot 的多Key操作。

4、Modules API：Redis 6中模块API开发进展非常大，因为Redis Labs为了开发复杂的功能，从一开始就用上Redis模块。Redis可以变成一个框架，利用Modules来构建不同系统，而不需要从头开始写然后还要BSD许可。Redis一开始就是一个向编写各种系统开放的平台。
