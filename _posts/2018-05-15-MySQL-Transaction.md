---
layout : post
title : MySQL Transaction
categories : MySQL
---

This post is for MySQL 5.7 InnoDB.

## Transaction Isolation Levels
 
  MySQL Innodb provides following isolation levels:

  1. Serializable
  2. Repeatable Read - default
  3. Read Committed
  4. Read Uncommited

  For above levels, item 1 and 4 are rarely used. While expert users might use item 3 as they push the boundaries of scalability with
  OLTP processing or during data warehousing operations where minor inconsistencies do not affect the aggregate results of large
  amounts of data.

  Note: select .. for update or select .. lock in share mode has the potential ot produce a deadlock depending on 
  the isolation level of transaction.
  
  ### Check isolation level
    
    Use `show variables like 'tx_isolation'`
  
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
