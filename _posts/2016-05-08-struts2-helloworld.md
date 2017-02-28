---
layout: post
title: struts2 helloworld
date: 2016-05-08 11:57
author: 2freesky
comments: true
categories: [struts]
---
struts version is 2.3.28

1.project structure

create a dynamic web project in Eclipse, then convert it to maven project and add sturts-core 2.3.28 dependency. the final project explorer looks like next :

<img class="alignnone size-full wp-image-196" src="https://2freesky.files.wordpress.com/2016/05/struts2-helloworld.png" alt="struts2-helloworld.PNG" width="230" height="488" />

2.View page

2.1.index.jsp
<blockquote>&lt;%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%&gt;
&lt;%-- the taglib directive tells the Servlet container that this page will be using the struts2 tags and that these tags will be preceded by s.--%&gt;
&lt;%@ taglib prefix="s" uri="/struts-tags" %&gt;
&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"&gt;
&lt;title&gt;Insert title here&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;h1&gt;Hello World from Struts2&lt;/h1&gt;
&lt;!-- if we want to use DMI, then the action name can be hello!nonExecute.action or hello!nonExecute here--&gt;
&lt;form action="hello"&gt;
Please enter your name :&lt;br/&gt;
&lt;input type="text" name="name" /&gt;
&lt;input type="submit" value="say hello" /&gt;
&lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;</blockquote>
DMI(dynamic method invocation) means run non-execute method of action class, the above form action represents it will run execute() of action class. However, if action name is hello.nonExecute, then it runs nonExecute() of action class.

2.2.result page
<blockquote>&lt;%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%&gt;
&lt;%@ taglib prefix="s" uri="/struts-tags" %&gt;
&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;

&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"&gt;
&lt;title&gt;Insert title here&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;!-- the s:property tag displays the value of action class property 'name' which is returned by the method getName() of HelloWorldAction --&gt;
Struts2 Hello World, &lt;s:property value="name" /&gt;
&lt;/body&gt;
&lt;/html&gt;</blockquote>
3.action class
<blockquote>public class HelloWorldAction {

private String name;

public String getName() {
return name;
}

public void setName(String name) {
this.name = name;
}

public String execute() {

return "success";
}

public String nonExecute() {

return "fail";
}

}</blockquote>
4.config files

4.1.struts.xml
<blockquote>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!-- next statement is required otherwise there is error when start tomcat --&gt;
&lt;!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
"http://struts.apache.org/dtds/struts-2.0.dtd"&gt;
&lt;struts&gt;
&lt;!-- set constant struts.devMode to true such that we can see some useful log messages --&gt;
&lt;constant name="struts.devMode" value="true" /&gt;
&lt;!-- In struts2 2.3.28, DynamicMethodInvocation must be set to true explicitly if we want to use DMI --&gt;
&lt;constant name="struts.enable.DynamicMethodInvocation" value="true" /&gt;
&lt;package name="helloworld" extends="struts-default"&gt;
&lt;!-- property 'method' of action tag means executing which method of action class, default is execute() --&gt;
&lt;action name="hello" class="cn.my.example.HelloWorldAction"
&gt;
&lt;result name="success"&gt;/HelloWorld.jsp&lt;/result&gt;
&lt;!-- if invoked method return success then we take the user to HelloWorld.jsp --&gt;
&lt;result name="fail"&gt;/error.jsp&lt;/result&gt;
&lt;/action&gt;
&lt;/package&gt;
&lt;/struts&gt;</blockquote>
<strong>Note : </strong>
<ul>
	<li><strong>&lt;constant name="struts.enable.DynamicMethodInvocation" value="true" /&gt; has to be declared in struts.xml in order to use DMI.</strong></li>
	<li><strong>For &lt;action name="" class=""&gt;, the class value is qualified name, that is class="cn.my.example.HelloWorld". While if struts is integrated with spring, its value is the id value of &lt;bean id=""&gt; which defined in spring applicationContext.xml</strong></li>
</ul>
4.2.web.xml
<blockquote>&lt;filter&gt;
&lt;filter-name&gt;struts2&lt;/filter-name&gt;
&lt;!-- &lt;filter-class&gt;org.apache.struts2.dispatcher.FilterDispatcher&lt;/filter-class&gt; --&gt;
&lt;!-- for struts2 2.3.28, the filter is next instead of above --&gt;
&lt;filter-class&gt;org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter&lt;/filter-class&gt;
&lt;/filter&gt;

&lt;filter-mapping&gt;
&lt;filter-name&gt;struts2&lt;/filter-name&gt;
&lt;url-pattern&gt;/*&lt;/url-pattern&gt;
&lt;/filter-mapping&gt;</blockquote>
