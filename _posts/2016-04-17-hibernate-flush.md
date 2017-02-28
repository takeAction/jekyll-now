---
layout: post
title: hibernate flush
date: 2016-04-17 17:53
author: 2freesky
comments: true
categories: [Hibernate]
---
1.What is flush

Flush session data to DB, when we call the session.flush(), the statements are executed in DB but it has not commit yet.  While commint() = flush() + commit.

2.flush mode
<ul>
	<li>manual : developer need to call session.flush() explicitly to do flush</li>
	<li>commit : the session is flushed when transaction.commit() is called</li>
	<li>auto       : default behavior, the session is <strong>sometimes</strong> flushed before query execution in order to ensure that queries never return stale state</li>
	<li>always   : the session is flushed before every query which is almost always unnecessary and inefficient</li>
</ul>
3.batch processing with flush

Consider following example of creating a large number of objects :
<blockquote>Session session = SessionFactory.openSession();
Transaction tx = session.beginTransaction();
for ( int i=0; i&lt;100000; i++ ) {
Employee emp = new Employee(.....);
session.save(emp);
}
tx.commit();
session.close();</blockquote>
above code may result in OutOfMerroyError somewhere, because hibernate caches all newly objects in the session level cache.

The solution can be used hibernate batch processing as follows :

First, add configuration to hibernate configuration file such as

&lt;property name="hibernate.jdbc.batch_size"&gt;40&lt;/property&gt;

this means hibernate executes every 40 rows as a batch.

Second, change corresponding code to
<blockquote>session.save(emp);
if( i % 40 == 0 ) {
session.flush();
session.clear();
}</blockquote>
4.my question

my question is : http://stackoverflow.com/questions/36662023/hibernate-auto-flush-incorrect, need to continue research.
