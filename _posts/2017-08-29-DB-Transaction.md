---
layout: post
title: DB Transaction
categories: database
---


#### Diagram


![_config.yml]({{ site.baseurl }}/images/db-transaction.png)

![_config.yml]({{ site.baseurl }}/images/lock-mode.png)

![_config.yml]({{ site.baseurl }}/images/lock.png)

#### Record/Update Lock

In MySQL InnoDB(from official document):

Record/Update Lock

A record lock is a lock on an index record. For example, SELECT c1 FROM t WHERE c1 = 10 FOR UPDATE; 

prevents any other transaction from inserting, updating, or deleting rows where the value of t.c1 is 10.

Record locks always lock index records, even if a table is defined with no indexes. 

For such cases, InnoDB creates a hidden clustered index and uses this index for record locking. 

#### Gap/Range Lock

Gap/Range Lock

A gap lock is a lock on a gap between index records, or a lock on the gap before the first or after the last index record. 

For example, SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE; prevents other transactions from 

inserting a value of 15 into column t.c1, whether or not there was already any such value in the column, 

because the gaps between all existing values in the range are locked.

A gap might span a single index value, multiple index values, or even be empty.

#### Select lock

##### SELECT ... LOCK IN SHARE MODE

Sets a shared mode lock on any rows that are read. Other sessions can read the rows, 

but cannot modify them until your transaction commits. If any of these rows were changed by another transaction 

that has not yet committed, your query waits until that transaction ends and then uses the latest values.

##### SELECT ... FOR UPDATE

For index records the search encounters, locks the rows and any associated index entries, 

the same as if you issued an UPDATE statement for those rows. 

Other transactions are blocked from updating those rows, from doing SELECT ... LOCK IN SHARE MODE, 

or from reading the data in certain transaction isolation levels. 

Consistent reads ignore any locks set on the records that exist in the read view. 

Locking of rows for update using SELECT FOR UPDATE only applies when autocommit is disabled
