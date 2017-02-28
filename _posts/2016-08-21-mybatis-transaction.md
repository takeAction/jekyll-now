---
layout: post
title: mybatis transaction
date: 2016-08-21 21:59
author: 2freesky
comments: true
categories: [mybatis]
---
1.Mybatis only

Developers need to call commit() explicitly such that changes can be stored in DB.  While for rollback(), we don't have to call it in most of the time as mybatis will do that for us if we don't call commit.

However if we need more fine grained control over a session where multiple commits and rollbacks are possible, we are able to use rollback.

2.Mybatis and spring integration

Mybatis-spring allows mybatis to participate in spring transactions.

In this case, there is no need to call commit or rollback explicitly and UnsupportedOperationException will thrown if we do that.

In order to use spring transaction, the additional changes as follows :
<ul>
	<li>add annotation @Transactional on the method which be used in transaction</li>
	<li>add following content which highlighted in red in spring config file</li>
</ul>
<blockquote>&lt;beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
<span style="color:#ff0000;">xmlns:tx="http://www.springframework.org/schema/tx"</span>
xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
<span style="color:#ff0000;">http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd</span>"&gt;

<span style="color:#ff0000;">&lt;tx:annotation-driven transaction-manager="transactionManager"/&gt;</span>

&lt;bean id="dataSource" class="org.springframework.jndi.JndiObjectFactoryBean"&gt;
&lt;property name="jndiName" value="java:/comp/env/jdbc/example" /&gt;
&lt;/bean&gt;

&lt;bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean"&gt;
&lt;property name="configLocation" value="classpath:mybatis-config.xml" /&gt;
&lt;property name="dataSource" ref="dataSource" /&gt;
&lt;/bean&gt;

&lt;bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate"&gt;
&lt;constructor-arg index="0" ref="sqlSessionFactory" /&gt;
&lt;/bean&gt;

<span style="color:#ff0000;">&lt;bean id="transactionManager" </span>
<span style="color:#ff0000;"> class="org.springframework.jdbc.datasource.DataSourceTransactionManager"&gt;</span>
<span style="color:#ff0000;"> &lt;property name="dataSource" ref="dataSource" /&gt;</span>
<span style="color:#ff0000;"> &lt;/bean&gt;</span>

&lt;/beans&gt;</blockquote>
