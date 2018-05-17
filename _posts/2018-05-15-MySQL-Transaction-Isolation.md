---
layout : post
title : MySQL Transaction Isolation
categories : MySQL
---

This post is for MySQL 5.7 InnoDB.

## Transaction Isolation Levels
 
  MySQL Innodb provides following isolation levels:

  1. Serializable, InnoDB implicitly converts all plain SELECT statements to `SELECT … LOCK IN SHARE MODE` if autocommit is disabled
  2. Repeatable Read - default     
  3. Read Committed
  4. Read Uncommited - can read uncommitted data, this known as dirty read
  
### Example
  
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
  
### Repeatable Read

   > Consistent reads within the same transaction read the snapshot established by the first read. 
   > This means that if you issue several plain (nonlocking) SELECT statements within the same transaction, 
   > these SELECT statements are consistent also with respect to each other.
   >
   > For locking reads (SELECT with FOR UPDATE or LOCK IN SHARE MODE), UPDATE, and DELETE statements, 
   > locking depends on whether the statement uses a unique index with a unique search condition, or a range-type search condition.
   > 
   > - For a unique index with a unique search condition, InnoDB locks only the index record found, not the gap before it.
   > - For other search conditions, InnoDB locks the index range scanned, using gap locks or next-key locks to block insertions by 
   > other sessions into the gaps covered by the range. 
   
### Read Committed

  > Each consistent read, even within the same transaction, sets and reads its own fresh snapshot.
  > 
  > For locking reads (SELECT with FOR UPDATE or LOCK IN SHARE MODE), UPDATE statements, and DELETE statements, 
  > InnoDB locks only index records, not the gaps before them, and thus permits the free insertion of new records next to locked 
  > records. Gap locking is only used for foreign-key constraint checking and duplicate-key checking.
  >
  > Because gap locking is disabled, phantom problems may occur, as other sessions can insert new rows into the gaps. 
  >
  > Using READ COMMITTED has additional effects:
  >
  > For UPDATE or DELETE statements, InnoDB holds locks only for rows that it updates or deletes. 
  > Record locks for nonmatching rows are released after MySQL has evaluated the WHERE condition. 
  > This greatly reduces the probability of deadlocks, but they can still happen.
  > 
  > For UPDATE statements, if a row is already locked, InnoDB performs a “semi-consistent” read, 
  > returning the latest committed version to MySQL so that MySQL can determine whether the row matches the 
  > WHERE condition of the UPDATE. If the row matches (must be updated), MySQL reads the row again 
  > and this time InnoDB either locks it or waits for a lock on it.
  
  Consider the following example, beginning with this table:
  
  ```
  CREATE TABLE t (a INT NOT NULL, b INT) ENGINE = InnoDB;
  INSERT INTO t VALUES (1,2),(2,3),(3,2),(4,3),(5,2);
  COMMIT;
  ```

  In this case, the table has no indexes, so searches and index scans use the hidden clustered index for record locking
  rather than indexed columns.

  Suppose that one session performs an UPDATE using these statements:
  
  ```  
  # Session A
  START TRANSACTION;
  UPDATE t SET b = 5 WHERE b = 3;
  ```
  
  Suppose also that a second session performs an UPDATE by executing this statement following those of the first session:

  ```
  # Session B
  UPDATE t SET b = 4 WHERE b = 2;
  ```  
  
  As InnoDB executes each UPDATE, it first acquires an exclusive lock for each row that it reads, and then determines whether to modify 
  it. If InnoDB does not modify the row, it releases the lock. Otherwise, InnoDB retains the lock until the end of the transaction. This 
  affects transaction processing as follows.

  When using the default REPEATABLE READ isolation level, the first UPDATE acquires an x-lock on each row that it reads and does not 
  release any of them:
  ```
  x-lock(1,2); retain x-lock
  x-lock(2,3); update(2,3) to (2,5); retain x-lock
  x-lock(3,2); retain x-lock
  x-lock(4,3); update(4,3) to (4,5); retain x-lock
  x-lock(5,2); retain x-lock
  ```
  The second UPDATE blocks as soon as it tries to acquire any locks (because first update has retained locks on all rows), and does not 
  proceed until the first UPDATE commits or rolls back:
  ```
  x-lock(1,2); block and wait for first UPDATE to commit or roll back
  ```

  If READ COMMITTED is used instead, the first UPDATE acquires an x-lock on each row that it reads and releases those for rows that it 
  does not modify:
  ```
  x-lock(1,2); unlock(1,2)
  x-lock(2,3); update(2,3) to (2,5); retain x-lock
  x-lock(3,2); unlock(3,2)
  x-lock(4,3); update(4,3) to (4,5); retain x-lock
  x-lock(5,2); unlock(5,2)
  ```

  For the second UPDATE, InnoDB does a “semi-consistent” read, returning the latest committed version of each row that it reads to MySQL 
  so that MySQL can determine whether the row matches the WHERE condition of the UPDATE:
  ```
  x-lock(1,2); update(1,2) to (1,4); retain x-lock
  x-lock(2,3); unlock(2,3)
  x-lock(3,2); update(3,2) to (3,4); retain x-lock
  x-lock(4,3); unlock(4,3)
  x-lock(5,2); update(5,2) to (5,4); retain x-lock
  ```

  However, if the WHERE condition includes an indexed column, and InnoDB uses the index, only the indexed column is considered when 
  taking and retaining record locks. In the following example, the first UPDATE takes and retains an x-lock on each row where b = 2. The 
  second UPDATE blocks when it tries to acquire x-locks on the same records, as it also uses the index defined on column b.

  ```
  CREATE TABLE t (a INT NOT NULL, b INT, c INT, INDEX (b)) ENGINE = InnoDB;
  INSERT INTO t VALUES (1,2,3),(2,2,4);
  COMMIT;

  # Session A
  START TRANSACTION;
  UPDATE t SET b = 3 WHERE b = 2 AND c = 3;

  # Session B
  UPDATE t SET b = 4 WHERE b = 2 AND c = 4;
  ```

  The effects of using the READ COMMITTED isolation level are the same as enabling the deprecated innodb_locks_unsafe_for_binlog 
  configuration option, with these exceptions:

  Enabling innodb_locks_unsafe_for_binlog is a global setting and affects all sessions, whereas the isolation level can be set globally 
  for all sessions, or individually per session.

  innodb_locks_unsafe_for_binlog can be set only at server startup, whereas the isolation level can be set at startup or 
  changed at runtime.

  READ COMMITTED therefore offers finer and more flexible control than innodb_locks_unsafe_for_binlog.
  
### Choose isolation level

  Using which isolation level is depends. From [MySQL]() :
  
  > You can enforce a high degree of consistency with the default REPEATABLE READ level, for operations on crucial data where ACID 
  > compliance is important. Or you can relax the consistency rules with READ COMMITTED or even READ UNCOMMITTED, in situations such as
  > bulk reporting where precise consistency and repeatable results are less important than minimizing the amount of overhead for 
  > locking. SERIALIZABLE enforces even stricter rules than REPEATABLE READ, and is used mainly in specialized situations, such as with 
  > XA transactions and for troubleshooting issues with concurrency and deadlocks.
  
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
  >
  > ISOLATION LEVEL level
  >   | READ WRITE
  >   | READ ONLY
  > 
  > level:
  >
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

**References:**

[MySQL](https://dev.mysql.com/doc/refman/5.7/en/set-transaction.html)

[Understanding MySQL Isolation Levels: Repeatable-Read](https://blog.pythian.com/understanding-mysql-isolation-levels-repeatable-read/)
