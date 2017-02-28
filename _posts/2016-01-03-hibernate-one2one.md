---
layout: post
title: Hibernate OneToOne
date: 2016-01-03 14:28
author: 2freesky
comments: true
categories: [Hibernate]
---
<h4 class="western">1. unidirectional with primary key</h4>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">1.1. table structure</span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">table employee : id int pk auto_increment, name varchar</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">table address    : id int pk auto_increment, zipcode varchar</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">1.2. POJO</span></span></p>

<blockquote class="western"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.OneToOne;
import javax.persistence.PrimaryKeyJoinColumn;</span></span></span></blockquote>
<blockquote class="western"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">@Entity
public class Employee {
@Id
@GeneratedValue(strategy=GenerationType.AUTO)
private int id;
private String name;
@OneToOne
@PrimaryKeyJoinColumn
private Address address;</span></span></span></blockquote>
<blockquote class="western"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">@Entity
public class Address {
@Id
@GeneratedValue(strategy=GenerationType.AUTO)
private int id;
private String zipCode;</span></span></span></blockquote>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">1.3 test class</span></span></p>

<blockquote>Employee e = new Employee();
e.setName("employee-A");
Address a = new Address();
a.setZipCode("777");
s.save(a);
s.save(e);//has to save or delete them separately

//However, if Employee is only @OneToOne, then the hql are
//insert into Address (zipCode) values (?)
//insert into Employee (address_id, name) values (?, ?), and in mysql, the employee has //another column 'áddress_id', and its value is the same as id, meanwhile add a foreign //key to point to id of address</blockquote>
<h4 class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">2. bidirectional with primary key</span></span></span></h4>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">2.1. table structure</span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">employee : id int pk auto_increment, name varchar</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">address  : id int pk, zipcode varchar</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">2.2. POJO</span></span></p>

<blockquote>@Entity
public class Employee{
@Id
@GeneratedValue(strategy=GenerationType.AUTO)
private int id;
private String name;
@OneToOne(cascade=CascadeType.ALL)
//if there is no @PrimaryKeyJoinColumn, then there is error : //org.hibernate.id.IdentifierGenerationException:
// null id generated for:class com.my.hibernate.association.model.Address
@PrimaryKeyJoinColumn
private Address address;

@Entity
public class Address{
@Id
@GeneratedValue(generator="foreigngen")
@GenericGenerator(strategy="foreign",name="foreigngen",
parameters=@Parameter(name="property",value="employee"))
private int id;
private String zipCode;
@OneToOne(mappedBy="address")
private Employee employee;</blockquote>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">2.3. test class</span></span></p>

<blockquote>Employee e = new Employee();
e.setName("henry");
Address a = new Address();
a.setZipCode("777");
e.setAddress(a);
a.setEmployee(e);
//the generated hql are :
//insert into Employee1 (name) values (?)
//insert into Address1 (zipCode, id) values (?, ?)
//if Employee without cascase=CascadeType.ALL, then the generated hql only is :
//insert into Employee1 (name) values (?)

s.save(e);

//In Address, if the annotation on Employee without 'mappedby', the the generated //hql are : insert into Employee1 (name) values (?)
//insert into Address1 (employee_id, zipCode, id) values (?, ?, ?)</blockquote>
<h4 class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">3.unidirectional with foreign key</span></span></span></h4>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">3.1. table structure</span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">author : id int pk auto increment, name varchar</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">book   : id int pk auto increment, name varchar,author_id foreign key reference id of author</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">3.2. POJO</span></span></p>

<blockquote>@Entity
public class Author {
@Id
@GeneratedValue(strategy=GenerationType.AUTO)
private int id;
private String name;

@Entity
public class Book {
@Id
@GeneratedValue(strategy=GenerationType.AUTO)
private int id;
private String name;
@OneToOne(cascade=CascadeType.ALL)
@JoinColumn(name="author_id")//name is the foreign key
private Author author;</blockquote>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">3.3. test class</span></span></p>

<blockquote>Author a = new Author();
a.setName("python");
Book b = new Book();
b.setName("history");
//if there is no next line, then it only generates one hql : insert into Book (author_id, name) values //(?, ?) and error : ERROR: Column 'author_id' cannot be null occurs
b.setAuthor(a);

//Hibernate: insert into Author (name) values (?)
//Hibernate: insert into Book (author_id, name) values (?, ?)
s.save(b);

//if Book without cascade, then there is error :
//Hibernate: insert into Book (author_id, name) values (?, ?)
//Column 'author_id' cannot be null
//if developer just delete Author, then error :
//Cannot delete or update a parent row: a foreign key constraint fails
//If delete Book, then hql looks like :
//select &lt;all fields of Book and Author&gt; from Book b left outer join Author a on b.author_id=a.id where b.id=?
//delete from Book where id=?
//delete from Author where id=?</blockquote>
<h4 class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">4. bidirectional with foreign key</span></span></span></h4>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">4.1. table structure</span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">It's the same as 3.1</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">4.2. POJO</span></span></p>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">It's similar to 3.2 except the additional information in Author :</span></span></span></p>

<blockquote class="western"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;"><span style="font-size:medium;">@OneToOne(mappedBy="author")
private Book book;</span></span></span></blockquote>
<p class="western" align="LEFT"><span style="color:#333333;"><span style="font-family:Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">4.3. test class</span></span></p>

<blockquote>Author a = new Author();
a.setName("frank");
Book b = new Book();
b.setName("document");
b.setAuthor(a);
//for bidirectional, if it is s.save(a), then only Author will be save
s.save(b);

//if developer just delete Author, then sql is :
//select &lt;all author and book fields&gt; from author a left outer join book b on a.id=b.author_id where //a.id=?
//and there is error : deleted object would be re-saved by cascade (remove deleted object from //associations)
//the solution is : add cascade=CascadeType.ALL in @OneToOne of Author
//if delete Book, then hql are:
//select &lt;all book and author fields&gt; from book b left outer join author a on b.author_id=a.id where //b.id=?
//select &lt;all book and author fileds&gt; from book b left outer join author a on b.author_id=a.id where //b.author_id=?
//delete from book where id=?
//delete from author where id=?</blockquote>
