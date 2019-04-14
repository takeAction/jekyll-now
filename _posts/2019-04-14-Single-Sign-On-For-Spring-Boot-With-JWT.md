---
layout : post
title : Single Sign On For Spring Boot With JWT
comments: true
categories : [Spring, Security]
---

### Single Sign On

Single Sign On, as known as SSO, means if user success login in one application, then he/she is able to access other
applications without login again.

### Flow

![_config.yml]({{ site.baseurl }}/images/sso.png)

Assume there are 3 applications : `app1`, `app2` and `authentication`.

#### Authentication

This application consist of :

1. login page
2. login controller, if username matches password, generate a `jwt`, create cookie with cookie name and jwt,
   set cookie `domain` and `path`, eventually add this cookie to `http servlet response`
3. redirect to original request

#### App1

1. set authentication login url
2. logout function to clear cookie with specified cookie name
3. a filter to check the user has login or not, if exist, proceed original request, otherwise redirect login page

#### App2

For simplicity, `App2` is the same as `App1`

### Code

Please refer [this](https://hellokoding.com/hello-single-sign-on-sso-with-json-web-token-jwt-spring-boot/) for detail
