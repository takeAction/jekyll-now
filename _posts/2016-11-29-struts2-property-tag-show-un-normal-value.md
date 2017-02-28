---
layout: post
title: struts2 property tag show un-normal value
date: 2016-11-29 22:31
author: 2freesky
comments: true
categories: [struts]
---
struts 2.0.9

The value is
<blockquote>AÜC</blockquote>
jsp
<blockquote>obj.value = "&lt;s:property value='customer.name'/&gt;"</blockquote>
obj represents an input field.

However, this input field value displayed as

[code]A&amp; #xDC;C[/code]

AÜC is got from DB and it is correct in struts action.  Meanwhile if I just assign "AÜC" to obj.value in jsp, it also can show right string.

Therefore the problem is from .

I tried to add escapeHtml='false' to "&lt;s:property value='customer.name' /&gt;", but the jsp throws an exception and stop running.

Eventually,

I found that if we put

[code]&lt;s:property value='customer.name' /&gt;[/code]

in element value attribute, e.g.

[code]&lt;input value=&quot;&lt;s:property value='customer.name' /&gt;&quot; /&gt;[/code]

,

then it's normal.

But if we use it in js, for example,

[code]

&lt;script&gt;

obj.value = &quot;&lt;s:property value='customer.name' /&gt;&quot;;

&lt;/script&gt;

[/code]

then it will show in-normal value, in this case we need to use jstl

[code]

&lt;script&gt;

obj.value = ${customer.name}&quot;;

&lt;/script&gt;

[/code]

