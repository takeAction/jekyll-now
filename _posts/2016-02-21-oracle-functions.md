---
layout: post
title: oracle functions
date: 2016-02-21 14:40
author: 2freesky
comments: true
categories: [Oracle]
---
<ol>
	<li>
<p class="western">nvl</p>
</li>
</ol>
<p class="western">nvl(column1, value1) ---&gt; if value of column1 is null, then return value1</p>

<ol start="2">
	<li>
<p class="western">nvl2</p>
</li>
</ol>
<p class="western">nvl2(column1, value1, value2) ---&gt; if value of column1 is not null, return value1, else return value2.</p>

<ol start="3">
	<li>
<p class="western">decode</p>
</li>
</ol>
<p class="western">decode(expression, value1, return1,value2,return2,..., default) ---&gt; if expression equals to value1, then return return1, if expression is the same as value2, then return return2 etc, else return default.</p>
