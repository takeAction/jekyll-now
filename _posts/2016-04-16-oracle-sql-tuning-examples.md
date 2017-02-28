---
layout: post
title: oracle sql tuning examples
date: 2016-04-16 12:52
author: 2freesky
comments: true
categories: [Oracle]
---
1.

there is one sql as follows :
<blockquote>select /*+index(create_time) */ * from h where h.result='F' and h.code&lt;&gt;'DO' and (h.code like :1 or h.code like :2 or h.code like :3) and h.create_time&gt;:4 and h.create_time&lt;:5</blockquote>
it has index on column 'create_time', :1, :2 and :3 can be %A,A% or %A%.

DBA told us that this is top sql, hence we need to optimize it.

My analysis are :
<ul>
	<li>it is useless that we create index on column 'code' since its value could be %A</li>
	<li>result only can be 'S' or 'F', but the 'S' occupy most data in DB(about 99%)</li>
	<li>'create_time' index is not good according to execute plan</li>
</ul>
Therefore, after removing hint and add index on column 'result', the efficient improved huge.

&nbsp;
