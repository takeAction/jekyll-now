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

```
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>;
<%-- the taglib directive tells the Servlet container that this page will be using the struts2 tags and that these tags will be preceded by s.--%>
<%@ taglib prefix="s" uri="/struts-tags" >
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h1>Hello World from Struts2&lt;/h1>
<!-- if we want to use DMI, then the action name can be hello!nonExecute.action or hello!nonExecute here--&gt;
<form action="hello">
Please enter your name :<br>
<input type="text" name="name" />
<input type="submit" value="say hello" />
</form>
</body>
</html>
```

DMI(dynamic method invocation) means run non-execute method of action class, the above form action represents it will run execute() of action class. However, if action name is hello.nonExecute, then it runs nonExecute() of action class.

2.2.result page

```
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<%@ taglib prefix="s" uri="/struts-tags" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<!-- the s:property tag displays the value of action class property 'name' which is returned by the method getName() of HelloWorldAction -->
Struts2 Hello World, <s:property value="name" />
</body>
</html>
```

3.action class

```
public class HelloWorldAction {

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

}
```

4.config files

4.1.struts.xml

```

<?xml version="1.0" encoding="UTF-8"?>
<!-- next statement is required otherwise there is error when start tomcat -->
<!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
"http://struts.apache.org/dtds/struts-2.0.dtd">
<struts>
<!-- set constant struts.devMode to true such that we can see some useful log messages -->
<constant name="struts.devMode" value="true" />
<!-- In struts2 2.3.28, DynamicMethodInvocation must be set to true explicitly if we want to use DMI -->
<constant name="struts.enable.DynamicMethodInvocation" value="true" />
<package name="helloworld" extends="struts-default">
<!-- property 'method' of action tag means executing which method of action class, default is execute() -->
<action name="hello" class="cn.my.example.HelloWorldAction"
>
<result name="success">/HelloWorld.jsp</result>
<!-- if invoked method return success then we take the user to HelloWorld.jsp -->
<result name="fail">/error.jsp</result>
</action>
</package>
</struts>
```

**Note : **
1. `<constant name="struts.enable.DynamicMethodInvocation" value="true" />` has to be declared in struts.xml in order to use DMI.
2. For `<action name="" class="">`, the class value is qualified name, that is `class="cn.my.example.HelloWorld"`. While if struts is integrated with spring, its value is the id value of `<bean id="">` which defined in spring applicationContext.xml
		
4.2.web.xml

```
<filter>
<filter-name>struts2</filter-name>
<!-- <filter-class>org.apache.struts2.dispatcher.FilterDispatcher</filter-class> -->
<!-- for struts2 2.3.28, the filter is next instead of above -->
<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>

<filter-mapping>
<filter-name>struts2</filter-name>
<url-pattern>/*</url-pattern>
</filter-mapping>
```
