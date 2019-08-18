---
layout : post
title : Prevent Form Duplicate Submit
comments: true
categories : [Java]
---

### Scenes

1. click `submit` button multiple times when network delay
2. refresh page after clicking submit button
3. after submit, click `back` button to back to previous page and submit again


### Solutions

There are two points to prevent duplicating form submission: front-end and back-end.

#### Front-end

Disable `submit` button via javascript after clicking it.

#### Back-end

##### Use `session`

1. create a `token` on the server-side and save it to `session`
2. send this `token` to the hidden field of form
3. submit this token together with the form data
4. if this token is not the same as server-side's or there is no token in the session or there is not token in the form, 
   then this is duplicate submit, 
5. otherwise it is normal submit and clear session's token.

##### Struts

In struts, there is `token` interceptor which can be used to handle multiple form submission problem.

### References

[使用Session防止表单重复提交](https://www.cnblogs.com/xdp-gacl/p/3859416.html)

[防止表单重复提交的方案](https://segmentfault.com/q/1010000011179023)

[Java后台防止客户端重复请求、提交表单](https://www.cnblogs.com/xhq1024/p/10650127.html)

[Spring MVC拦截器+注解方式实现防止表单重复提交](https://www.cnblogs.com/grefr/p/6088517.html)
