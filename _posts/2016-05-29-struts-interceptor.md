---
layout: post
title: struts interceptor
date: 2016-05-29 16:23
author: 2freesky
comments: true
categories: [Struts2]
---

### what is interceptor

 Interceptor is used for doing additional handling before/after calling action method.
 
### config struts.xml

there are two ways to define interceptors :

#### Method 1

```xml
<interceptors> 
 <interceptor name="first" class="example.my.interceptor.MyInterceptor" />                
 <interceptor name="second"        class="example.my.interceptor.AnotherInterceptor" /> 
</interceptors>
<action name="empinfo" class="example.my.i18n.EmpInfo" method="execute"> 
 <result name="input">/index.jsp</result> 
 <interceptor-ref name="timer" /> 
 <interceptor-ref name="first" /> 
 <interceptor-ref name="second" /> 
</action>
```

wherein, 'timer' is built-in interceptor which is used for recording the execution time for action method, so there is no need to declare it in `<interceptors>`

For custom interceptors, we are supposed to declare in `<interceptors>`

#### Method 2

```xml
<interceptors> 
 <interceptor name="first" class="example.my.interceptor.MyInterceptor" /> 
 <interceptor name="second" class="example.my.interceptor.AnotherInterceptor" /> 
 <interceptor-stack name="myStack">     
 <interceptor-ref name="second" />     
 <interceptor-ref name="first" /> 
 </interceptor-stack> 
</interceptors>
<action name="empinfo" class="example.my.i18n.EmpInfo" method="execute"> 
 <result name="success">/success.jsp</result> 
 <interceptor-ref name="myStack" /> 
</action>
```

### create custom interceptors

custom interceptor has to extend AbstractInterceptor or implement Interceptor :

```java
public class MyInterceptor extends AbstractInterceptor{
private static final long serialVersionUID = -3169055473033684813L;

@Override 
public String intercept(ActionInvocation invocation) throws Exception { 
System.out.println(" MyInterceptor pre-processing before calling execute()");

invocation.invoke();

System.out.println("MyInterceptor post-process after calling execute()"); 
return null; }

}
```

```java
public class AnotherInterceptor implements Interceptor{
private static final long serialVersionUID = 1761916853142898972L;

@Override public void destroy() { }

@Override public void init() { }

@Override public String intercept(ActionInvocation invocation) throws Exception { 
System.out.println(" AnotherInterceptor pre-processing before calling execute()");

invocation.invoke();

System.out.println("AnotherInterceptor post-process after calling execute()"); 
return null; }

}
```

the invocation.invoke() will call other interceptor or actual action method.

The order of interceptor calling is the same as order of interceptor definition, take 2.1 for example, the output is :

```
MyInterceptor pre-processing before calling execute()
AnotherInterceptor pre-processing before calling execute()

action method is exectuting !
AnotherInterceptor post-process after calling execute()
MyInterceptor post-process after calling execute()
```
