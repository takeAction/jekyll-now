---
layout : post
title : Session
comments: true
categories : Java
---

Http protocol is a stateless, that is each request is a new request. 

However we need to recognize the user in a period of time.  

This is why there is session.

## Http Session

![_config.yml]({{ site.baseurl }}/images/http-session.png)

Generally, session is per user and per app.

There are four techniques used in session:

1. Cookies
2. Hidden Form Field
3. URL Rewriting
4. HttpSession

### Cookie

![_config.yml]({{ site.baseurl }}/images/session-cookie.png)

There are 2 types of cookies in servlets.

- Non-persistent cookie
- Persistent cookie

**Note:** Whenever a browser sends a request to a web server, all cookies set by the same server are automatically sent in the request.

#### Non-persisten Cookie

It is valid for single session only. 

It is removed each time when user close the browser.

#### Persistent Cookie

It is valid for multiple session . 

It is not removed when user closes the browser except user logout or signout.

### Hidden Form Field

The info is stored in hidden form field.

### URL Rewriting

User info is appended at the url.

### HttpSession

The `HttpSession` object represents a user session which contains user info across multiple HTTP requests.

![_config.yml]({{ site.baseurl }}/images/httpsession.png)

If you connect from two different browsers, 
the server will create two session ids.

**Note: There is no way for any 3rd party to guess which session maps to which user since that information is stored safely on the server and is not made public.**

#### How to send

At times, users/browsers may not accept cookies from certain servers. 

To deal with this case, web servers also support passing the session identifier in the URL (URL rewriting).

Initially, the server doesn't know if the client has blocked cookies or not.

So it sends the JSESSIONID to the client in two ways:
- In a cookie.
- As a URL parameter (e.g. http://www.abc.com;jsessionid=123xyz).

When the server gets a subsequent request from the same client:

If the request contains the JSESSIONID cookie, it means that the client does accept cookies. 

If not, the server understands that cookies are blocked and it continues to use the URL parameter approach ("URL rewriting"). 

#### What happened if browser closed

1. Cookie approach: 

   The JSESSIONID cookie is a "session only" cookie, so the browser deletes it as soon as the browser is closed. 

2. URL rewriting approach: 

   If you copied the URL with the JSESSIONID, close the browser, open a new browser window and use the copied URL. It will work as long as the session has not timed out. This also poses a security risk (if someone else knows the full URL with the JSESSIONID, they could use it, and if the session was still active, they could do stuff on behalf of someone else). This is one of the reasons why cookies are preferred over URL rewriting.

## Session and Cluster

In a cluster environment, user session need to be prevent from missing.

The solution to this problem would be one of:
- Do not use session attributes.
- Use a session database
- Use sticky session, where a user is always sent to the same server, throughout the whole session.

## Share Session

### Why cannot share session in same tomcat

https://softwareengineering.stackexchange.com/questions/178290/why-cant-wars-share-session-info

> "In accordance with the servlet 2.3 API specification, the session manager supports session scoping by Web module only. Only servlets in the same Web module can access the data associated with a particular session." This can be seen again in HttpSession - "Session information is scoped only to the current web application (ServletContext), so information stored in one context will not be directly visible in another."

### How to Share

1. `Spring Session` can be used to share session

2. Single-Sign-On

3. Other approaches.

   https://stackoverflow.com/questions/665941/any-way-to-share-session-state-between-different-applications-in-tomcat

   https://spring.io/projects/spring-session

   http://lukaszbudnik.blogspot.com/2009/06/session-sharing-in-apache-tomcat.html

   https://tomcat.apache.org/tomcat-8.0-doc/config/context.html

   https://stackoverflow.com/questions/334425/different-war-files-shared-resources
