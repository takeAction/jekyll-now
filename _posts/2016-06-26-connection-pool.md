---
layout: post
title: connection pool
date: 2016-06-26 11:08
author: 2freesky
comments: true
categories: [Mybatis, Spring]
---

### What is connection pool

  When application needs to operate the database, it has to create a connection and close it after finishing. 
  
  This is costly and waste resources as well as affect the performance for each user/request.
  
  Therefore, this is why connection pool comes. It is a cache of database connection so that connection can be reused. 
  
  Connection is placed in the pool after it is created or the operation finished. 
  It is used again so that a new connection does not have to be established.
  
  Generally, the pool has min/initial and max connection, if it reaches the initial number, then a new connection will be created and placed in pool. 
  While it reaches max number, then new operations has to wait current operation release the connection.

### Products

  There are some connection pool products, such as c3p0, DBCP.
  
### How to use

  Take c3p0 with spring and mybatis for example.

  The context.xml of tomcat looks like next :
  
  ```xml
  <Resource name="jdbc/test" auth="Container"
  type="com.mchange.v2.c3p0.ComboPooledDataSource"
  factory="org.apache.naming.factory.BeanFactory"
  user="test"
  password=""
  jdbcUrl="jdbc:mysql://localhost:3306/test"
  driverClass="com.mysql.jdbc.Driver"
  minPoolSize="2"
  initialPoolSize="3"
  maxPoolSize="10" />
  ```

  Spring config file is :
  
  ```xml
  <bean id="dataSource" class="org.springframework.jndi.JndiObjectFactoryBean">
    <property name="jndiName" value="java:/comp/env/jdbc/test" />
  </bean>

  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="configLocation" value="classpath:config/mybatis-config.xml" />
    <property name="dataSource" ref="dataSource" />
  </bean>
  
  <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <constructor-arg index="0" ref="sqlSessionFactory" />
  </bean>
  ```
