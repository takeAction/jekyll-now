---
layout:post
title : Redis Basic
categories : Redis
---

Redis 可通过两种方式来持久存储数据：RDB 和 AOF。
RDB 持久性按照指定的间隔对您的数据集执行时间点快照。它不是非常耐久，而且您可能会丢失一些数据，但它非常快。
AOF 的持久性要长得多，而且记录了服务器收到的每个写入操作。这些写入操作在服务器启动时重新执行，重新构建原始数据集。
在查询 Redis 时，将从内存中获取数据，绝不会从磁盘获取数据

Redis 采用了一种客户端/服务器模型，借用该模型来监听 TCP 端口并接受命令。
Redis 中的所有命令都是原子性的，所以您可以从不同的客户端处理同一个键，没有任何争用条件。

受支持的数据类型包括：

字符串
列表
集合
有序集
哈希值

Redis 是一个多效用工具，对许多用例很有用，
这些用例包括缓存、消息队列（Redis 原生支持发布/订阅）、短期应用程序数据（比如 Web 会话、Web 页面命中计数）等。

Redis 中的列表是一个有序的字符串集合，您可以向其中添加任意数量的（惟一或非惟一）元素。除了向列表添加元素和从中获取元素的操作之外，
Redis 还支持对列表使用取出、推送、范围和其他一些操作。

在 Redis 中，列表从 0 开始。

作为一个例子，假设您希望维护一个单词列表（无论是否惟一），并获取您最近添加到系统中的三个单词。

要将单词添加到列表中，可以使用 LPUSH 命令，它将一个或多个值附加到列表前部：

redis> LPUSH words austerity
(integer) 1
redis> LPUSH words socialism moratorium socialism socialism
(integer) 5
使用 LPUSH，最近添加的单词位于列表顶部，以前添加的单词会在后面。可使用 LRANGE 命令查看列表中顶部的三个单词：

redis> LRANGE words 0 2
1) "socialism"
2) "socialism"
3) "moratorium"
要获得列表的长度，可使用 LLEN 命令：

redis > LLEN words
(integer) 5
要从列表中删除元素，可使用 LREM 命令。例如，使用此命令删除所有 socialism：

redis> LREM words 0 socialism
(integer) 2
要删除列表，必须删除 words 键：

redis 127.0.0.1:6379> DEL words
(integer) 1

集合
集合（set）是惟一元素的无序集合。要将一个元素添加到一个集合中，可使用 SADD 命令；要获取一个集合的所有成员，可使用 SMEMBERS 命令。作为一个例子，假设我希望维护所有添加到系统中的惟一单词的集合（称为 uniquewords）。可以看到，因为我使用了 SET，所以无法将同一个单词 (socialism) 添加两次：

redis> SADD uniquewords austerity
(integer) 1
redis> SADD uniquewords pragmatic
(integer) 1
redis> SADD uniquewords moratorium
(integer) 1
redis> SADD uniquewords socialism
(integer) 1
redis> SADD uniquewords socialism
(integer) 0
要查看 uniquewords 集合中的所有单词，可使用 SMEMBERS 命令：

redis 127.0.0.1:6379> SMEMBERS uniquewords
1) "moratorium"
2) "austerity"
3) "socialism"
4) "pragmatic"
也可以在多个集合上执行 intersection 和 union 命令。我将创建另一个集合，称为 newwords，并向其中添加一些元素：

redis 127.0.0.1:6379> SADD newwords austerity good describe strange
(integer) 4
为了找出两个集合中的所有共同元素（uniquewords 和 newwords），我执行了 SINTER 命令：

redis 127.0.0.1:6379> SINTER uniquewords newwords
1) "austerity"
为了合并多个集合，我使用了 SUNION 命令。可以看到，单词 austerity 仅被添加了一次。

redis 127.0.0.1:6379> SUNION uniquewords newwords
1) "austerity"
2) "strange"
3) "describe"
4) "socialism"
5) "pragmatic"
6) "good"
7) "moratorium"

有序集
有序集是可基于一个分数进行排序并且仅包含惟一元素的集合。每次您向集合中添加一个值，您都会提供一个用于排序的分数。

例如，假设您希望基于单词的长度对一组单词进行排序。使用 ZADD 命令将一个元素添加到一个有序集中，使用语法 ZADD 键分数 值。使用 ZRANGE 命令按分数查看一个有序集的元素。

redis> ZADD wordswithlength 9 austerity
(integer) 1
redis> ZADD wordswithlength 7 furtive
(integer) 1
redis> ZADD wordswithlength 5 bigot
(integer) 1
redis> ZRANGE wordswithlength 0 -1
1) "bigot"
2) "furtive"
3) "austerity"
要获得有序集的大小，可使用 ZCARD 命令：

redis 127.0.0.1:6379> ZCARD wordswithlength
(integer) 3
哈希值
哈希值允许您针对一个哈希值存储一个键-值对。此选项可能对您希望保存具有多个属性的对象的情形很有用，就像以下示例一样：

redis> HSET user:1 name shekhar
(integer) 1
redis> HSET user:1 lastname gulati
(integer) 1
redis> HGET user:1
redis> HGET user:1 name
"shekhar"
redis> HGETALL user:1
1) "name"
2) "shekhar"
3) "lastname"
4) "gulati"

RedisConnectionFactory 是一个用来与 Redis 建立连接的线程安全的连接工厂，RedisConnection 是连接到 Redis 的一个短期、非线程安全的连接。
RedisConnection 提供了与 Redis 命令的一对一映射，而 RedisConnectionFactory 提供了有助于消除样板代码的便捷方法。
RedisConnectionFactory 使不同 Redis 客户端 API 之间的切换就像定义一个 bean 那么简单。
我们将对样例应用程序使用 JedisConnectionFactory，但也可使用其他任何 ConnectionFactory 变体。

flushAll() 命令从数据库中删除所有键，而 flushDb() 仅删除当前数据库中的键。

Jedis是Redis官方推出的一款面向Java的客户端，提供了很多接口供Java语言调用。可以在Redis官网下载，当然还有一些开源爱好者提供的客户端，
如Jredis、SRP等等，推荐使用Jedis。

Spring Data Redis

　　SDR是Spring官方推出，可以算是Spring框架集成Redis操作的一个子框架，封装了Redis的很多命令，可以很方便的使用Spring操作Redis数据库，
  Spring对很多工具都提供了类似的集成，如Spring Data MongDB…

　　这三个究竟有什么区别呢？可以简单的这么理解，Redis是用ANSI C写的一个基于内存的Key-Value数据库，而Jedis是Redis官方推出的面向Java的Client，
  提供了很多接口和方法，可以让Java操作使用Redis，而Spring Data Redis是对Jedis进行了封装，集成了Jedis的一些命令和方法，
  可以与Spring整合
  
 第一个是在redis.xml中配置redistemplate的时候，同时配置了两个Serializer：keySerializer实现了StringRedisSerializer，valueSerializer实现了JdkSerializationRedisSerializer。

　　一、为什么要使用Serializer

　　因为redis是以key-value的形式将数据存在内存中，key就是简单的string，key似乎没有长度限制，不过原则上应该尽可能的短小且可读性强，无论是否基于持久存储，key在服务的整个生命周期中都会在内存中，因此减小key的尺寸可以有效的节约内存，同时也能优化key检索的效率。

　　value在redis中，存储层面仍然基于string，在逻辑层面，可以是string/set/list/map，不过redis为了性能考虑，使用不同的“encoding”数据结构类型来表示它们。(例如：linkedlist，ziplist等)。

　　所以可以理解为，其实redis在存储数据时，都把数据转化成了byte[]数组的形式，那么在存取数据时，需要将数据格式进行转化，那么就要用到序列化和反序列化了，这也就是为什么需要配置Serializer的原因。
  
  SDR支持的序列化策略：

（详细可查阅API文档）

JdkSerializationRedisSerializer：
StringRedisSerializer：
JacksonJsonRedisSerializer：
OxmSerializer：
　　其中JdkSerializationRedisSerializer和StringRedisSerializer是最基础的序列化策略，其中“JacksonJsonRedisSerializer”与“OxmSerializer”都是基于stirng存储，因此它们是较为“高级”的序列化(最终还是使用string解析以及构建java对象)。

　　基本推荐使用JdkSerializationRedisSerializer和StringRedisSerializer，因为其他两个序列化策略使用起来配置很麻烦，如果实在有需要序列化成Json和XML格式，可以使用java代码将String转化成相应的Json和XML。
  
  使用Serializer

　　在本项目中，是在配置文件中直接配置了相应的Serializer，key用的是StringRedisSerializer，value用的是JdkSerializationRedisSerializer，因为在此项目中，key为userId,为String类型，value为user为java类，即POJO，所以使用JdkSerializationRedisSerializer。

　　在redistemplate中直接配置Serializer当然比较方便，因为在后面想redis中存取数据时，就不用再次配置Serializer，但是这仅限于只有一种数据类型的情况，比如在本项目中只有<String userId,User user>类型的数据需要存储，如果有多种数据类型时，在配置文件中配置就显得不方便了，那么我们可以在存取数据时，即Service的实现类存取数据操作时分别指定相应的Serializer。

　　所以在编程时有两种选择：

　　　　1.在redistemplate中配置Serializer（本项目即采用这种方式）

ValueOperations<String, User> valueops = redisTemplate
                .opsForValue();
valueops.set(user.getId(), user);
 

　　　　2.不在redistemplate中配置Serializer，而是在Service的实现类中单独指定Serializer。就如同UserOperationsServiceImpl.java注释的代码：


1 boolean result = redisTemplate.execute(new RedisCallback<Boolean>() {
2      public Boolean doInRedis(RedisConnection redisConnection) throws DataAccessException { 
3          RedisSerializer<String> redisSerializer = redisTemplate .getStringSerializer(); 
4          byte[] key = redisSerializer.serialize(user.getId());
5          byte[] value = redisSerializer.serialize(user.getName()); 
6          return redisConnection.setNX(key, value); } }); 
7     return result;
8 }

在spring中进行redis存储，如果没有对key和value进行序列化，保存到redis中会出现乱码

redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步

2.jedis
jedis是Redis的Java版客户端实现，也是官方推荐的Java版客户端。它封装了对Redis的各种操作，并且支持事务、管道及有jedis自身实现的分布式。

3.Spring Data Redis
Spring Data是Spring框架中的一个主要项目，目的是为了简化构建基于Spring框架应用的数据访问，包括非关系数据库、Map-Reduce框架、云数据服务等，另外也包含对关系数据库的访问支持。
Spring Data Redis是Spring Data项目中的一个主要模块，实现了对jedis客户端API的高度封装，使对Redis的操作更加便捷。

