---
layout: post
title : Redis Basic
categories : Redis
---

### Redis

  Redis is a in-memory data structure store, used as a database, cache and message broker.
  
  - It uses disk only for persistence.
  - It is written in C.
  - All redis operations are atomic
  
#### Data Types

  Redis supports 5 data types :
  
  - String, a string value can be at max 512 megabytes in length
  - Hash, is map between the string field and string value
  
  ```Shell
  HMSET mykey name "student1" 
  age 20 
  OK 
  redis 127.0.0.1:6379> HGETALL mykey  
  1) "name" 
  2) "student1" 
  3) "age" 
  4) "20" 
  ```
  
  - List,  is simply list of strings
  
  ```Shell
  redis 127.0.0.1:6379> LPUSH listName value1 
  (integer) 1 
  redis 127.0.0.1:6379> LPUSH listName value2 
  (integer) 2 
  redis 127.0.0.1:6379> LPUSH listName value3 
  (integer) 3 
  ```
  
  In the above example, three values are inserted in Redis list named 'listName' by command 'LPUSH'.
  
  - Set, is unordered collection of unique strings, `SADD setName keyValue` is used to add element to set 
  - Sorted set, the difference with Set is, every member in it is associated with a score, that is used in order to take
    the sorted set ordered, from the smallest to greatest
    
    ```Shell
    redis 127.0.0.1:6379> ZADD setName 1 value1 
    (integer) 1 
    redis 127.0.0.1:6379> ZADD setName 2 value2 
    (integer) 1 
    redis 127.0.0.1:6379> ZADD setName 3 value3 
    (integer) 1 
    ```
    
    three values are inserted with its score in sorted set name 
  
#### Commands

  Some redis commands as follows:

  - SET, set key and value
  - GET, get value of the key
  - DEL, deletes the key if it exists
  - EXPIRE, sets the expiry of the key after the specified time
  - TYPE, returns the data type of the value stored in the key
  - AUTH password, use password to enter redis
  
#### Publish/Subscribe

  Pub/Sub implements the messaging system where the senders(called publisher) send the message, while the 
  recivers(subscriber) receive it. The link by which the message is transferred is called channel.
  
  In redis, a client can subscribe any number of channels.
  
  `SUBSCRIBE redisChat` - one client subscribes a channel named 'redisChat'.
  
  `PUBLISH redisChat "Hello World"` - publish message to channed named 'redisChat'.
  
#### Transaction

  A group of commands can be executed in  a single step.
  
  - All commands in a transaction are sequentially executed as a single isolated operation. It is not possible that
    a request issued by another client is served in the middle of the execution of a redis transaction.
  - It is atomic.
  
  ```Shell
  redis 127.0.0.1:6379> MULTI 
  OK 
  ......list of command
  
  redis 127.0.0.1:6379> EXEC  
  ```
  
  Transaction begins with `MULTI`, ends with `EXEC`
  
#### Backup

  Redis backup is done by command `SAVE`, **dump.rdp** file will be created in your redis directory.
  
  ```
  127.0.0.1:6379> SAVE  
  OK 
  ```
  
  To restore redis data, make sure redis backup file (dump.rdp) is in your redis install directory, then start the server.
  
  Command `CONFIG get dir` will show your redis directory.
  
#### Pipelining

  Pipelining means client can send multiple requests to the server without waiting for the replies at all, and finally
  read the replies in a single step.
  
#### Partitioning

  It means split data into multiple redis instances.
  
  There are two types:
  
  - Range partitioning, e.g. user 1 -10 will go into instance 1, user 11-20 go into instance 2.
  - Hash partitioning, a hash function is used to convert the key into a number and then the data stored in different 
    instances.

### Clients

  Redis has some clients for Java, such as Jedis, JRedis. Take Jedis for example,
  
  ```Java
  //Connecting to Redis server on localhost 
  Jedis jedis = new Jedis("localhost");  
  jedis.set("mykey", "Hello World"); 
  System.out.println(jedis.get("mykey")); 
  ```
  
  `JedisPool pool = new JedisPool(new JedisPoolConfig(), "localhost")`, JedisPool is a threadsafe pool.
  
  ```
  Jedis jedis = null;
  try {
      jedis = pool.getResource();
      .....
   } finally {
      // You have to close jedis object. If you don't close then
      // it doesn't release back to pool and you can't get a new
      // resource from pool.
      if (jedis != null) {
          jedis.close();
      }
   }
   /// ... when closing your application:
   pool.close();
  ```

### Spring Data Redis

  It aslo called SDR which is a part of spring data, provides easy configuration and access to redis from spring 
  application. It offers both low level and high level abstractions for interacting with the store.
