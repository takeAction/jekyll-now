---
layout: post
title: mybatis transaction
date: 2016-08-21 21:59
author: 2freesky
comments: true
categories: [Mybatis]
---

### Mybatis only

Developers need to call commit() explicitly such that changes can be stored in DB.  While for rollback(), we don't have to call it in most of the time as mybatis will do that for us if we don't call commit.

However if we need more fine grained control over a session where multiple commits and rollbacks are possible, we are able to use rollback.

### Mybatis and spring integration

Mybatis-spring allows mybatis to participate in spring transactions.

In this case, there is no need to call commit or rollback explicitly and UnsupportedOperationException will thrown if we do that.

In order to use spring transaction, the additional changes as follows :

- add annotation @Transactional on the method which be used in transaction
- spring config file looks like
  
  ```xml
  <beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"     
         xmlns:tx="http://www.springframework.org/schema/tx" xsi:schemaLocation="http://www.springframework.org/schema/beans 
   http://www.springframework.org/schema/beans/spring-beans-4.0.xsd http://www.springframework.org/schema/tx 
   http://www.springframework.org/schema/tx/spring-tx-4.0.xsd">

  <tx:annotation-driven transaction-manager="transactionManager"/>

  <bean id="dataSource" class="org.springframework.jndi.JndiObjectFactoryBean"> 
  <property name="jndiName" value="java:/comp/env/jdbc/example" /> 
  </bean>

  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean"> 
  <property name="configLocation" value="classpath:mybatis-config.xml" /> 
  <property name="dataSource" ref="dataSource" /> 
  </bean>

  <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate"> 
  <constructor-arg index="0" ref="sqlSessionFactory" /> 
  </bean>

  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager"> 
  <property name="dataSource" ref="dataSource" /> 
  </bean>

  </beans>
  ```
