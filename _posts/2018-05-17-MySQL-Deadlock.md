---
layout : post
title : MySQL Deadlock
categories : MySQL
---

MySQL 5.7 InnoDB

### Example

  ```
  T1                                                                    T2
  SELECT * FROM t WHERE id = 1 LOCK IN SHARE MODE;
                                                                        UPDATE/DELETE t WHERE id = 1;
  UPDATE/DELETE t WHERE id = 1;
  ```
  
  The update/delete operation of T2 requires an `X` lock. The lock cannot be granted because it is incompatible with the `S` lock 
  that T1 holds, so the request goes on the queue of lock requests for the row and T2 blocks.
  
  Then T1 also want to update/delete same record, but there is error thrown : 
  
  **Deadlock found when trying to get lock; try restarting transaction**
  
  Deadlock occurs here because T1 needs an `X` lock to update/delete the row. 
  However, that lock request cannot be granted because T2 already has a request for an `X` lock 
  and is waiting for T1 to release its `S` lock. 
  
  Nor can the `S` lock held by T1 be upgraded to an `X` lock because of the prior request by T2 for an `X` lock. 
  
  As a result, InnoDB generates an error for one of the clients and releases its locks. 

### Detection

  Deadlock detection is enabled by default.
  
  When deadlock is detected, InnoDB tries to pick small transactions to roll back, where the size of a transaction is determined
  by the number of the rows inserted, updated or deleted.
  
  On high concurrency systems, deadlock detection can cause a slowdown when numerous threads wait for the same lock. 
  
  At times, it may be more efficient to disable deadlock detection and rely on the `innodb_lock_wait_timeout` setting for transaction 
  rollback when a deadlock occurs. 
  For more detail about this property, please refer to [doc](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_lock_wait_timeout).
  
  Deadlock detection can be disabled using the `innodb_deadlock_detect` configuration option. Note that this property introduced from version 5.7.15.

### Handle

 1. Use `SHOW ENGINE INNODB STATUS` command to determine the cause of the most recent deadlock. 
 
 2. Enabling the `innodb_print_all_deadlocks` configuration option to collect more debug info which is recorded in the MySQL error log. 
    Disable this option when you are finished debugging.

 3. Just try again if deadlock occur.

 4. Keep transactions small and short in duration to make them less prone to collision.

 5. Commit transactions immediately after making a set of related changes to make them less prone to collision. 
    In particular, do not leave an interactive mysql session open for a long time with an uncommitted transaction.

 6. If you use locking reads (`SELECT ... FOR UPDATE` or `SELECT ... LOCK IN SHARE MODE`), try using a lower isolation level 
    such as `READ COMMITTED`.

 7. Use same order to do operation
 
 8. Add well-chosen indexes to your tables. Then your queries need to scan fewer index records and consequently set fewer locks. 

 9. Use less locking. If you can afford to permit a SELECT to return data from an old snapshot, do not add the clause `FOR UPDATE` or 
    `LOCK IN SHARE MODE` to it. 

 10. If nothing else helps, serialize your transactions with table-level locks. 
 
     ```
     SET autocommit=0;
     LOCK TABLES t1 WRITE, t2 READ, ...;
     ... do something with tables t1 and t2 here ...
     COMMIT;
     UNLOCK TABLES;
     ```   
     
**References**

[innodb-deadlocks](https://dev.mysql.com/doc/refman/5.7/en/innodb-deadlocks.html)
