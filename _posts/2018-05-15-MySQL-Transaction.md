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
  
### Phantom read and non-repeatable read
  
  Non-repeatable read : same row has different value for same select in one transaction.
    
  Phantom read : rows added or deleted for same select in one transaction.
    
  `Repeatable Read` has no phantom read but it has `phantom write`.
    
  Let's look at following example. The table A has original data like:
    
  ```
  ID     TEXT
  1      T1
  4      R3
  5      T3
  ```
    
 NOW we have two transaction `T1` and 'T2`:
    
 ![_config.yml]({{ site.baseurl }}/images/repeatable-read2.png)
    
 From this snapshot, we can see that : second select of T1 cannot see new data T4 with id is 6 and T5 with id is 7.
 
 But update of T1 can success and then third select of T1 can retrieve only updated row.
  
### Check isolation level
    
  Use `show variables like 'tx_isolation'` or `SELECT @@GLOBAL.tx_isolation, @@tx_isolation;`
  
### Change isolation level

  > SET [GLOBAL | SESSION] TRANSACTION
  > transaction_characteristic [, transaction_characteristic] ...
  > 
  > transaction_characteristic:
  > ISOLATION LEVEL level
  >   | READ WRITE
  >   | READ ONLY
  > 
  > level:
  >   REPEATABLE READ
  >   | READ COMMITTED
  >   | READ UNCOMMITTED
  >   | SERIALIZABLE
  
  OR 
    
  in an option file. For example, in option file,
    
  ```
  [mysqld]
  transaction-isolation = REPEATABLE-READ
  ```
      
## Transaction Access Mode

  From MySQL doc:
  
  > By default, a transaction takes place in read/write mode, with both reads and writes permitted to tables used in the transaction.   
  > This mode may be specified explicitly using an access mode of READ WRITE.
  >
  > If the transaction access mode is set to READ ONLY, changes to tables are prohibited. 
  > This may enable storage engines to make performance improvements that are possible when writes are not permitted.
  >
  > It is not permitted to specify both READ WRITE and READ ONLY in the same statement.
  > 
  > In read-only mode, it remains possible to change tables created with the TEMPORARY keyword using DML statements. 
  > Changes made with DDL statements are not permitted, just as with permanent tables.
  >
  > The READ WRITE and READ ONLY access modes also may be specified for an individual transaction using the START TRANSACTION statement.

**References:**

[MySQL](https://dev.mysql.com/doc/refman/5.7/en/set-transaction.html).
[Understanding MySQL Isolation Levels: Repeatable-Read](https://blog.pythian.com/understanding-mysql-isolation-levels-repeatable-read/)
