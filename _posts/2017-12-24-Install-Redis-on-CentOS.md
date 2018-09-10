---
layout: post
title: Install Redis
categories: [Redis, Linux]
---

### Install Redis

  1. sudo yum update
  2. sudo yum install epel-release
  3. sudo yum update
  4. sudo yum install redis
  5. start redis by **sudo systemctl start redis**
  
#### Start Redis on boot

  **sudo systemctl enable redis**
  
### Verify Installation

  Run the command **redis-cli ping** in terminal, if redis is running, it will return **PONG**.
  
### View redis status

  **sudo systemctl status redis.service**
  
### Restart redis

  ```shell_session 
  sudo systemctl restart redis
  ```
  
### Set key-value to redis

  ```shell_session
  
  root@redis-node:~# redis-cli
  127.0.0.1:6379> set besthost "Globo.Tech"
  OK
  127.0.0.1:6379> get besthost
  "Globo.Tech"
  
  ```
  
### Config redis

  redis configuration file is **/etc/redis.conf**
  
  There is a article describes some redis configuration, following sections Persistence Option and Basic System Tuning 
  are part of this site, please refer [it](https://linode.com/docs/databases/redis/install-and-configure-redis-on-centos-7/).
  
#### Persistence Options
  
  Redis provides two options for disk persistence:
  
  1. Point-in-time snapshots of the dataset, made at specified intervals (RDB)
  
  2. Append-only logs of all the write operations performed by the server (AOF)
  
  For the greatest level of data safety, consider running both persistence methods. 
  
  Because the Point-in-time snapshot persistence is enabled by default, you only need to set up AOF persistence.
  
#### Basic System Tuning

  To improve Redis performance, set the Linux kernel overcommit memory setting to 1 `sudo sysctl vm.overcommit_memory=1`,
  then add `vm.overcommit_memory = 1` to `/etc/sysctl.conf`
  
#### Bound redis to local IP

  We need to ensure that Redis is bounded to the local IP. Unless you are running a full Redis cluster, this is the best way 
  to secure your Redis store from unauthorized access.

  - nano /etc/redis.conf
  - Search for the line that begins with “bind”, changing it as follows: bind 127.0.0.1
  
### Access redis from other computer

  1. modify firewall to make it allow redis port
  
     ```shell_session
     firewall-cmd --add-port=6379/tcp --permanent
     firewall-cmd --reload
     ```
  
  2. modify redis conf file
  
     set `protected-mode = no` and comment `bind 127.0.0.1` in `/etc/redis.conf`, restart redis
   
  3. use `nc` command at other linux based computer
  
     **nc -v --ssl redis.mydomain.com 6379** 
     or 
     **nc -v redis.mydomain.com 6379**
