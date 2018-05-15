---
layout : post
title : MySQL Transaction
categories : MySQL
---

This post is for MySQL 5.7 InnoDB.

## Transaction Isolation Levels
 
  MySQL Innodb provides following isolation levels:

  1. Serializable, InnoDB implicitly converts all plain SELECT statements to `SELECT … LOCK IN SHARE MODE`
  2. Repeatable Read - default
  3. Read Committed
  4. Read Uncommited - can read uncommitted data, this known as dirty read

  For above levels, item 1 and 4 are rarely used. 
  
  ![_config.yml]({{ site.baseurl }}/images/repeatable-read.png)
  
  For above diagram, the select is **plain select(nonlocking)**:
  
  - If in `Repeatable Read`, the result set of second select is the same as first select, even `T2` insert/update/delete data and commit
        
  - If in `Read Committed`, `T2` insert/update/delete data and commit, then second select of `T1` will see changed data, thus its result
    set is different from first select.
    
  Isolation level is for different transactions, in one transaction, latter select can see former changed. 
  
  Take above diagram for example, in `T1`, if insert/update/delete is placed between first select and second select of `T1`,
  then second select will see changed data after executing insert/update/delete.

  Note: select .. for update or select .. lock in share mode has the potential ot produce a deadlock depending on 
  the isolation level of transaction.
  
  ### Check isolation level
    
    Use `show variables like 'tx_isolation'` or `SELECT @@GLOBAL.tx_isolation, @@tx_isolation;`
  
  ### Change isolation level

    User can set isolation level temporary or default.
  
   #### Set temporary level
  
      `SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;`
  
   #### Set default level
  
      Use the `--transaction-isolation=level` option to mysqld on the command line or in an option file. For example, in option file,
    
      ```
      [mysqld]
      transaction-isolation = REPEATABLE-READ
      ```
      
## Transaction Access Mode

Please refer [MySQL](https://dev.mysql.com/doc/refman/5.7/en/set-transaction.html) for detail.
