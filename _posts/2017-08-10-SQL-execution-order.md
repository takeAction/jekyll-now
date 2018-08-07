---
layout: post
title: SQL execution order
categories: Database
---

1. Logical processing order

    - FROM
    - JOIN
    - WHERE
    - GROUP BY
    - HAVING
    - SELECT
    - DISTINCT
    - ORDER BY
    - LIMIT/TOP
    
In some databases, alias created in `SELECT` cannot be used in earlier steps. This restriction is imposed because the column value may
not yet have been determined when the clause that appear before the `SELECT` clause are evaluated(such as the `WHERE` clause).
While in other database, e.g. MYSQL, alias in the `SELECT` is allowed in `GROUP BY` OR `HAVING`.

Expression alias cannot be used by other expression within the same select list. This is because the logic order in which the expression 
is evaluated does not matter and is not guaranteed. For example, this select clause might not work as expected, and is therefore,
not supported: `select a+1 as x, x+1 as y`. However, in MYSQL, you can write it like `select a+1 as x, (select x)+1 as y`.

2. Actual processing order

   The real execution order is what you see when you look at the estimate or actual query plan.
   
   For example, in MYSQL, if explain plan looks like next:
   
   table
   EB
   SV2
   CB
   
   then it means table EB will be get first, then SV2 and last is CB.
   
   However in ORACLE, take following plan for example:
   
   ```
   Execution Plan
   ----------------------------------------------------------
   0      SELECT STATEMENT Optimizer=CHOOSE (Cost=168 Card=82 Bytes=3936)
   1    0   TABLE ACCESS (BY INDEX ROWID) OF 'EMP' (Cost=2 Card=1 Bytes=12)
   2    1     NESTED LOOPS (Cost=168 Card=82 Bytes=3936)
   3    2       MERGE JOIN (CARTESIAN) (Cost=4 Card=82 Bytes=2952)
   4    3         TABLE ACCESS (FULL) OF 'DEPT' (Cost=2 Card=1 Bytes=10)
   5    3         BUFFER (SORT) (Cost=2 Card=82 Bytes=2132)
   6    5           TABLE ACCESS (FULL) OF 'BONUS' (Cost=2 Card=82 Bytes=2132)
   7    2       INDEX (RANGE SCAN) OF 'IX_EMP_01' (NON-UNIQUE) (Cost=1 Card=1)
   ```
   the rules of reading explain plan are:
   - diagram a picture according to the indent
   - look at the diagram, from left to right
   - if two nodes are in the same level, then the left will be executed first
   
   for above plan, its diagram is:
   ```
               1
               2
           3      7
       4      5
                6
   ```
   Hence, the execution order is : 4, 6, 5, 3, 7, 2, 1
   
   
