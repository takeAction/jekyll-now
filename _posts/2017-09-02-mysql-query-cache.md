---
layout: post
title: MySQL Query Cache
categories: Database
---

> The query cache is deprecated as of MySQL 5.7.20, and is removed in MySQL 8.0.

![_config.yml]({{ site.baseurl }}/images/mysql-query-cache.png)

It also works within transactions(except serializable isolation level because it use lock in share mode) when using InnoDB 
or the select query on a view.

Identical statement : statements are execately the same(byte for byte), 
e.g. SELECT * FROM tbl_name and Select * FROM tbl_name are not the same.
Even statements are same, if use different databases, different protocol versions or different default character sets
are considered different queries.

The query cache is not used for queries of the following type:
- Queries that are a subquery of an outer query
- Queries executed within the body of a stored function, user defined function, trigger etc
- Contains some built-in functions or tables, e.g. BENCHMARK(), INFORMATION_SCHEMA
- It refers to user variables or local stored program variables
- It is of any of the following forms: select ... lock in share mode, select ... for update etc
- It uses TEMPORARY table
- It generates warnings
- The user has column level privilege for any of the involved tables

Be cautious about the size of it, larger not mean better.  Sizes in tens of megabytes are usually beneficial, while
hundreds of megabytes might not be. Due to the need for threads to lock the cache during updates, you may see lock 
contention issues with a large cache.

Meanwhile, too small is not good since it needs a minimun size of about 40 KB to allocate its structure(The exact size
depends on system architecture).

select SQL_CACHE id, name from customer.
The query result is cached if it is cachable and the value of the query_cache_type system variable is ON or DEMAND.

select SQL_NO_CACHE id, name from customer.
The server does not use the query cache. It neither checks the query cache to see whether the result is already cached,
nor does it cache the query result.(Due to the limitation in the parser, a space character must be precede and follow
the SQL_NO_CACHE keyword. A non-space such as a newline causes the server to check the query cache to see whether the 
result is already cached)

### Variables/Status related with query cache
```
have_query_cache             show variables like 'have_query_cache', always is YES even if query cache is disabled
query_cache_size             set it is 0 to disable query cache, also need to set query_cache_type to 0 in this case
query_cache_type             0 or OFF means prevents caching or retrieval of cached results
                             1 or ON enables caching except those statements include SQL_NO_CACHE
                             2 or DEMAND causes caching only for statements contain SQL_CACHE
                             Individual clients can control cache behavior for thier own connection like:
                             set session query_cache_type=0
query_cache_min_res_unit     Memory allocation is costly(timewise), the query cache allocate blocks 
                             with a minimun size given by this variable. So when one block is filled, 
                             a new block is allocated. When a query is executed, the last result block is trimmed
                             to the actual data size so that unused memory is freed.
Qcache                       SHOW STATUS LIKE 'Qcache%'
RESET QUERY CACHE            removes all query results, FLUSH TABLES also does this
```

