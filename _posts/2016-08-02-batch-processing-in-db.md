---
layout: post
title: Batch processing in DB
date: 2016-08-02 16:11
author: 2freesky
comments: true
categories: [Mybatis]
---

### What

  Group sql statements into a batch and submit them with one call to DB, rather than send each statement one by one, 
  this is batch processing.

### Why

  Batch processing can reduce the amount of communication overhead, thereby improving  performance. 
  
  It is faster than sending them one by one without waiting for each one to finish, and the DB may be 
  execute some of them in parallel.
  
### How

  In mybatis, we have two approaches to implement batch processing:
  
  1. if integrate mybatis and spring
     
     ```xml
     <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg index="0" ref="sqlSessionFactory" />

        <constructor-arg index="1" value="BATCH" />

     </bean>
     ```
     
     then in code :

     ```java
     for() {

        sqlSession.update();

     }
     ```
     
  2. without spring
  
     ```java
     SqlSession session = factory.openSession(ExecutorType.BATCH);			
     for(Child c : childs) {

       session.update("updateChild", c);

     }

     session.commit();
     ```
