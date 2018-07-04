---
layout : post
title : SQL Processing
comments: true
categories : SQL
---

## DDL and DML

  - DDL
    
    DDL is Data Definition Language, such as `create table`, `alter table`
    
  - DML
  
    DML is Data Manipulation Language, e.g. `insert`, 'update`, 'delete`

## SQL processing

  Take Oracle for example, the following figure depicts the general stages of SQL processing.
  
  ![_config.yml]({{ site.baseurl }}/images/sql-processing-stage.PNG)
  
  We can see that it consist of parsing, optimization, row source generation and exectuion.
  
### Parsing

  During the parsing stage, the database performs the following checks.
  
#### Syntax Check
  
#### Semantic Check
  
  The semantics of a statement are its meaning. A semantic check determines whether a statement is meaningful, 
  for example, whether the objects and columns in the statement exist.
    
#### Shared Pool Check
  
  The database uses a hashing algorithm to generate a hash value for every SQL statement, this value also called SQL ID. 
     
  The same statement in a single instance or in different instances has the same Shash value.

  When a user submits a SQL statement, the database searches the shared SQL area to see 
  if an existing parsed statement has the same hash value. 

  A SQL statement can have multiple plans in the shared pool. Typically, each plan has a different hash value. 
     
### Optimization

  Create one or more execution plan.
  
  The database never optimizes DDL unless it includes a DML component such as a subquery that requires optimization.

### Row Source Generation

  Database the best execution plan from the optimizer and produces a binary format execution plan, 
  when executed by the SQL engine, produces the result set. 

### Execution

  During execution, the SQL engine executes each step according to the execution plan.
  
  For example, for sql
  
  ```sql
  SELECT 
    C.CREDIT_ID,
    C.CUSTOMER_ID AS CREDIT_CUSTOMER_ID,
    CU.CUSTOMER_ID,
    CU.CODE,
    CU.PARENT_ID,
    B.BILLING_ID,
    B.BILLING_NO,
    B.PAYER_ID
  FROM
    CREDIT C
        LEFT JOIN
    CUSTOMER CU ON CU.CUSTOMER_ID = C.CUSTOMER_ID
        OR CU.PARENT_ID = C.CUSTOMER_ID
        LEFT JOIN
    BILLING B ON B.PAYER_ID = CU.CUSTOMER_ID
  order by C.CREDIT_ID
  ```
  
  its execution plan looks like :
  
  ![_config.yml]({{ site.baseurl }}/images/execution-plan.png)
  
  It has 3 step, and sql engine will execute each step and combine their result sets.
  
#### Process order

  For a sql statement, the order is:
  
  1. From. 
  
     Execute Cartesian product for left table and right table, create a virtual table VT1.
  
  2. Join On. 
  
     Fileter the VT1 by on condition, create a table VT2.
  
  3. Left/Right Join. 
  
     If it is left/right join, add unmatch left table or right table to VT2, create VT3.
  
  4. If there are multiple tables in from, **repeat step 1 - step 3**
  
  5. Where
  
     Filter by where condition, produce VT4.
     
  6. Group by
  
  7. Cube or Rollup
  
  8. Having
  
  9. Select
  
  10. Distinct
  
  11. Order by
  
  12. Limit
  
  Each step produces a virtual table, this virtual table is the input of next step.
  
  The order of tables are determined by execution plan, instead of their order in sql statement.

## Hard parse and Soft parse

  1. Hard parse
  
     If database cannot reuse existing execution plan, then it must build a new one.
     
     > The database always performs a hard parse of DDL
  
  2. Soft parse
  
     Resue the existing execution plan instead of creating a new one.
     
     For example. a same prepared statement executes soft parse, while non prepared statement use hard parse.
  
     Even same statement, it still can use hard parse if semantic or environment is different, please refer to the reference
  at the end of this post.
  
  Soft parse skip syntax check, semantic check, sql optimization and row source generation.

## References

  [Oracle Sql Processing](https://docs.oracle.com/database/121/TGSQL/tgsql_sqlproc.htm#TGSQL190)
