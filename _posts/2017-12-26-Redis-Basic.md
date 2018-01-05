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

### Clients

  Redis has some clients for Java, such as Jedis, JRedis

### Spring Data Redis

  It aslo called SDR.
  


  
