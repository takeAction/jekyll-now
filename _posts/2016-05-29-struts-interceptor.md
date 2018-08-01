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
 
<h1>2.config struts.xml</h1>
there are two ways to define interceptors :
<h2>2.1.</h2>
<blockquote> &lt;interceptors&gt;
&lt;interceptor name="first" class="example.my.interceptor.MyInterceptor" /&gt;                &lt;interceptor name="second"        class="example.my.interceptor.AnotherInterceptor" /&gt;
&lt;/interceptors&gt;

&lt;action name="empinfo" class="example.my.i18n.EmpInfo" method="execute"&gt;
&lt;result name="input"&gt;/index.jsp&lt;/result&gt;
&lt;interceptor-ref name="timer" /&gt;
&lt;interceptor-ref name="first" /&gt;
&lt;interceptor-ref name="second" /&gt;
&lt;/action&gt;</blockquote>
wherein, 'timer' is built-in interceptor which is used for recording the execution time for action method, so there is no need to declare it in &lt;interceptors&gt;.

For custom interceptors, we are supposed to declare in &lt;interceptors&gt;.
<h2>2.2.</h2>
<blockquote>&lt;interceptors&gt;
&lt;interceptor name="first" class="example.my.interceptor.MyInterceptor" /&gt;
&lt;interceptor name="second" class="example.my.interceptor.AnotherInterceptor" /&gt;
<em><strong>&lt;interceptor-stack name="myStack"&gt;</strong></em>
<em><strong>    &lt;interceptor-ref name="second" /&gt;</strong></em>
<em><strong>    &lt;interceptor-ref name="first" /&gt;</strong></em>
<em><strong> &lt;/interceptor-stack&gt;</strong></em>
&lt;/interceptors&gt;

&lt;action name="empinfo" class="example.my.i18n.EmpInfo" method="execute"&gt;
&lt;result name="success"&gt;/success.jsp&lt;/result&gt;
<em><strong>&lt;interceptor-ref name="myStack" /&gt;</strong></em>
&lt;/action&gt;</blockquote>
<h1>3.create custom interceptors</h1>
custom interceptor has to extend AbstractInterceptor or implement Interceptor :
<blockquote>public class MyInterceptor extends AbstractInterceptor{

private static final long serialVersionUID = -3169055473033684813L;

@Override
public String intercept(ActionInvocation invocation) throws Exception {
System.out.println(" MyInterceptor pre-processing before calling execute()");

invocation.invoke();

System.out.println("MyInterceptor post-process after calling execute()");
return null;
}

}</blockquote>
&nbsp;
<blockquote>public class AnotherInterceptor implements Interceptor{

private static final long serialVersionUID = 1761916853142898972L;

@Override
public void destroy() {
}

@Override
public void init() {
}

@Override
public String intercept(ActionInvocation invocation) throws Exception {
System.out.println(" AnotherInterceptor pre-processing before calling execute()");

invocation.invoke();

System.out.println("AnotherInterceptor post-process after calling execute()");
return null;
}

}</blockquote>
the invocation.invoke() will call other interceptor or actual action method.

The order of interceptor calling is the same as order of interceptor definition, take 2.1 for example, the output is :
<blockquote>MyInterceptor pre-processing before calling execute()

AnotherInterceptor pre-processing before calling execute()
<h4>action method is exectuting !</h4>
AnotherInterceptor post-process after calling execute()

MyInterceptor post-process after calling execute()</blockquote>
