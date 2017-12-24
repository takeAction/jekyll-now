---
layout: post
title: Redis
categories: [Redis]
---

### Install Redis

  1. sudo yum update
  2. sudo yum install epel-release
  3. sudo yum update
  4. sudo yum install redis
  5. start redis by `sudo systemctl start redis`
  
#### Start Redis on boot

  sudo systemctl enable redis
  
### Verify Installation

  Run the command `redis-cli ping` in terminal, if redis is running, it will return **PONG**.
  
### View redis status

  sudo systemctl status redis.service
  
### Restart redis

  sudo systemctl restart redis
  
### Set key-value to redis

  ```
  root@redis-node:~# redis-cli
  127.0.0.1:6379> set besthost "Globo.Tech"
  OK
  127.0.0.1:6379> get besthost
  "Globo.Tech"
  ```
  
### Config redis

  redis configuration file is **/etc/redis.conf**
  ```
  requirepass your_redis_master_password
  # line 142: save caching Datase on Disk
  # the default settings below means like follows
  # after 900 sec if at least 1 key changed
  # after 300 sec if at least 10 keys changed
  # after 60 sec if at least 10000 keys changed
  # if you'd like to disable this function, comment out all lines fo "save ***" or specify [save ""]
  save 900 1
  save 300 10
  save 60 10000
  # line 392: authorization password
  requirepass password
  # line 501: alternative persistence mode ("yes" means enabled)
  # if enabled, Redis loses high performance but get more safety
  appendonly no
  # line 531: if enabled "appendonly yes" when wirting data on Disk
  # no means do not fsync by Redis (just let the OS flush the data)
  # appendfsync always
  appendfsync everysec
  # appendfsync no
  ```
  
### Redis cluster

  redis master/slave
  
### Bound redis to local IP

  We need to ensure that Redis is bounded to the local IP. Unless you are running a full Redis cluster, this is the best way 
  to secure your Redis store from unauthorized access.

  - nano /etc/redis.conf
  - Search for the line that begins with “bind”, changing it as follows: bind 127.0.0.1
  
### Access redis from other computer

  - firewall-cmd --add-port=6379/tcp --permanent
  - firewall-cmd --reload
