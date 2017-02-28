---
layout: post
title: struts2 cannot get right url parameter value
date: 2016-08-11 22:46
author: 2freesky
comments: true
categories: [struts]
---
In struts action, I used ServletActionContext.getRequest().getParameter()  to get url parameter value, the parameter value is encrypted and encoded by urlencoder which looks like 'a%2F%3D%3D'.

However, when use ServletActionContext.getRequest().getParameter(), it will convert the 3%D or 2F% to other characters like +, hence its value is not the same as url parameter value.  Therefore, we cannot decrypt it to original string.
