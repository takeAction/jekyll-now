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
  
  The update/delete operation of T2 requires an X lock. The lock cannot be granted because it is incompatible with the S lock 
  that T1 holds, so the request goes on the queue of lock requests for the row and T2 blocks.
  
  Then T1 also want to update/delete same record, but there is error thrown : 
  
  **Deadlock found when trying to get lock; try restarting transaction**
  
  Deadlock occurs here because T1 needs an X lock to update/delete the row. 
  However, that lock request cannot be granted because T2 already has a request for an X lock 
  and is waiting for T1 to release its S lock. 
  
  Nor can the S lock held by T1 be upgraded to an X lock because of the prior request by T2 for an X lock. 
  
  As a result, InnoDB generates an error for one of the clients and releases its locks. 

### Detection and Rollback

### Minimize and handle

**References**

[innodb-deadlocks](https://dev.mysql.com/doc/refman/5.7/en/innodb-deadlocks.html)
