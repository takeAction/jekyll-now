---
layout: post
title: javascript regular expression
date: 2016-10-19 23:16
author: 2freesky
comments: true
categories: [javascript]
---
In js, there is a function "replace(source, target)" which means replace the source of original with target.

Wherein source can be a plain string or regular expression, e.g.
<blockquote>var s = " You can click this link to access detail : ${url=http://192.168.0.1/TEST/a?} ";
var t = s.replace(/\${url=([^+]+)}/,"");</blockquote>
if source is regular expression :
<ul>
 	<li>it CANNOT be enclosed by ""</li>
 	<li>regular expression has to be start with / and end with /, while /.../g represents replace all match string</li>
 	<li>$ needs to be escaped, while { or } doesn't</li>
</ul>
