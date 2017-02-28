---
layout: post
title: Sql injection
date: 2016-06-11 10:48
author: 2freesky
comments: true
categories: [mybatis, Sql]
---
<h3>1.What is sql injection</h3>
Take following code for example,
<blockquote>   Statement st = conn.createStatement();
String query = "select * from table where userName='" + user + "'";
st.executeQuery(query);</blockquote>
For above code, if value of user is : a' or '1' = '1, then the sql will be : select * from table         where userName='a' or '1'='1', as we know, this where clause always return true,  this is      sql  injection.
<h3>2.How to prevent it in Java</h3>
We can use PreparedStatement with placeholder to solve this problem, the code looks            like :
<blockquote> PreparedStatement pstmt = con.prepareStatement("select * from table where userName=?");
pstmt.setString(1,user);</blockquote>
the setXX method do all the validation and escaping the special character.

Now, if user still input value : a' or '1' = '1, the sql is : select * from table where                       userName='a\, or \'1\'=\'1'

When we use ORM tool like mybatis, it uses PreparedStatement if #{} be used in sql.

However, ${} is interpreted as string substitution, hence it has risk of sql injection.
