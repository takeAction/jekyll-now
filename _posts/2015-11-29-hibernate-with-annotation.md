---
layout: post
title: Hibernate with annotation
date: 2015-11-29 04:39
author: 2freesky
comments: true
categories: [Hibernate]
---
Entity is :
<blockquote>import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Student{

@Id
@GeneratedValue(strategy=GenerationType.AUTO)
private int id;
private String name;
private int age;

//getters and setters

}</blockquote>
hibernate.cfg.xml looks like :
<blockquote>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!DOCTYPE hibernate-configuration PUBLIC
"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd"&gt;

&lt;hibernate-configuration&gt;
&lt;!-- SessionFactory is a global factory responsible for a particular database.
If you have several databases, for easier startup you should use several &lt;session-factory&gt; configurations in several configuration files. --&gt;
&lt;session-factory&gt;

&lt;!-- Database connection settings for MySQL--&gt;
&lt;property name="connection.driver_class"&gt;com.mysql.jdbc.Driver&lt;/property&gt;
&lt;property name="connection.url"&gt;jdbc:mysql://localhost/test&lt;/property&gt;
&lt;property name="connection.username"&gt;root&lt;/property&gt;
&lt;property name="connection.password"/&gt;
&lt;!-- SQL dialect for MySQL--&gt;
&lt;property name="dialect"&gt;org.hibernate.dialect.MySQLDialect&lt;/property&gt;

&lt;!-- JDBC connection pool (use the built-in) --&gt;
&lt;!-- The built-in Hibernate connection pool is in no way intended for production use. It
lacks several features found on any decent connection pool. --&gt;
&lt;property name="connection.pool_size"&gt;1&lt;/property&gt;

&lt;!-- Enable Hibernate's automatic session context management --&gt;
&lt;property name="current_session_context_class"&gt;thread&lt;/property&gt;

&lt;!-- Disable the second-level cache --&gt;
&lt;property name="cache.provider_class"&gt;org.hibernate.cache.internal.NoCacheProvider&lt;/property&gt;

&lt;!-- Echo all executed SQL to stdout --&gt;
&lt;property name="show_sql"&gt;true&lt;/property&gt;

&lt;!-- Drop and re-create the database schema on startup --&gt;
&lt;property name="hbm2ddl.auto"&gt;update&lt;/property&gt;
&lt;mapping class="com.my.hibernate.basic.Student" /&gt;
&lt;/session-factory&gt;

&lt;/hibernate-configuration&gt;</blockquote>
HibernateUtil.class :
<blockquote>public class HibernateUtil {

private static final SessionFactory sessionFactory = buildSessionFactory();

private static SessionFactory buildSessionFactory() {
//A org.hibernate.Session represents a single-threaded unit of work.
//The org.hibernate.SessionFactory is a thread-safe global object that is instantiated once.
// Create the SessionFactory from hibernate.cfg.xml
try {
Configuration config = new Configuration();
config.configure("resources/hibernate.cfg.xml");
ServiceRegistry registry = new StandardServiceRegistryBuilder()
.applySettings(config.getProperties()).build();
return config.buildSessionFactory(registry);

}catch(Throwable ex) {
System.out.println("Initial create session factory failed : "+ex);
throw new ExceptionInInitializerError(ex);
}
}

public static SessionFactory getSessionFactory() {
return sessionFactory;
}
}</blockquote>
following is main class :
<blockquote>public static void main(String[] args) {

Session session = HibernateUtil.getSessionFactory().openSession();
session.beginTransaction();
Student student = new Student();
student.setAge(31);
student.setName("next");
session.save(student);
session.getTransaction().commit();

session.close();
}</blockquote>
If DB column name are the same as field name, then there is no need to use @Column(name="") as well as @Table(name="")

@Id @GeneratedValue means how to generate primary key.  There are four options in JPA :

<a href="https://2freesky.files.wordpress.com/2015/11/tmp.png" rel="attachment wp-att-59"><img class="alignnone size-medium wp-image-59" src="https://2freesky.files.wordpress.com/2015/11/tmp.png?w=300" alt="tmp" width="300" height="240" /></a>

As above mentioned, both AUTO and IDENTITY are ok in this entity since this id is auto increment in mysql.
