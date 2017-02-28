---
layout: post
title: sql group by
date: 2016-02-21 15:20
author: 2freesky
comments: true
categories: [Sql]
---
<h4>1.group by</h4>
The group by statement is used to group the result-set by one or more columns, e.g.

<a href="https://2freesky.files.wordpress.com/2016/02/group_by.png" rel="attachment wp-att-81"><img class="alignnone size-medium wp-image-81" src="https://2freesky.files.wordpress.com/2016/02/group_by.png?w=300" alt="group_by" width="300" height="88" /></a>

if the sql is : select rate_type, effective_date from table group by rate_type, effective_date, then the result looks like next :

SC    02/10/15

SC    12/08/16

<em><strong>Note : the columns in select have to be in group by or in aggregate functions</strong></em>, in other words, if we want to select rate_type, effective_date, approve_time in a sql with group by, then the sql should be :

'select rate_type, effective_date, approve_time from table group by rate_type,effective_date,approve_time'

or

'select rate_type, effective_code, max(approve_time) from table group by rate_type,effective_date'

<strong>Note : the column with varchar type also can be used in aggregate functions besides the number</strong>

and it is wrong if it is 'select rate_type, effective_date, approve_time from table group by rate_type,effective_date'.
<h4>2.having</h4>
The having clause is used with group by usually and the aggregate functions following it.

It was added to sql because where clause could not be used with aggregate functions.
