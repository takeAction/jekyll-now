---
layout: post
title: spring mvc
date: 2016-04-10 17:55
author: 2freesky
comments: true
categories: [spring, spring]
---
1.Flow

<a href="https://2freesky.files.wordpress.com/2016/04/spring_mvc.png" rel="attachment wp-att-132"><img class="alignnone size-medium wp-image-132" src="https://2freesky.files.wordpress.com/2016/04/spring_mvc.png?w=300" alt="spring_mvc" width="300" height="174" /></a>

&nbsp;

2.code

2.1 web.xml
<blockquote>&lt;display-name&gt;springMVCExample&lt;/display-name&gt;
&lt;welcome-file-list&gt;
&lt;welcome-file&gt;index.jsp&lt;/welcome-file&gt;
&lt;/welcome-file-list&gt;

&lt;servlet&gt;
&lt;!-- Once DispatcherServlet is initialized, it will look for file
&lt;servlet-name&gt;-servlet.xml in WEB-INF folder of web application
--&gt;
&lt;servlet-name&gt;helloworld&lt;/servlet-name&gt;
&lt;servlet-class&gt;org.springframework.web.servlet.DispatcherServlet&lt;/servlet-class&gt;
&lt;load-on-startup&gt;1&lt;/load-on-startup&gt;
&lt;/servlet&gt;

&lt;servlet-mapping&gt;
&lt;servlet-name&gt;helloworld&lt;/servlet-name&gt;
&lt;!-- this servlet will handle all http request --&gt;
&lt;url-pattern&gt;/&lt;/url-pattern&gt;
&lt;/servlet-mapping&gt;</blockquote>
2.2 spring mvc config xml
<blockquote>&lt;beans xmlns="http://www.springframework.org/schema/beans"
xmlns:mvc="http://www.springframework.org/schema/mvc"
xmlns:context="http://www.springframework.org/schema/context"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd"&gt;

&lt;context:component-scan base-package="cn.example.spring.mvc.controller" /&gt;

&lt;bean id="viewResolver" class="org.springframework.web.servlet.view.UrlBasedViewResolver"&gt;
&lt;property name="viewClass" value="org.springframework.web.servlet.view.JstlView" /&gt;
&lt;property name="prefix" value="/WEB-INF/jsp/" /&gt;
&lt;property name="suffix" value=".jsp" /&gt;
&lt;/bean&gt;

&lt;bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver"&gt;

&lt;!-- this property means if there is SpringException thrown, then ExceptionPage.jsp will be called
&lt;property name="exceptionMappings"&gt;
&lt;props&gt;
&lt;prop key="cn.example.spring.mvc.exception.SpringException"&gt;
ExceptionPage
&lt;/prop&gt;
&lt;/props&gt;
&lt;/property&gt; --&gt;
&lt;!-- next line means the defautl exception page is error.jsp --&gt;
&lt;property name="defaultErrorView" value="error" /&gt;
&lt;/bean&gt;

&lt;/beans&gt;</blockquote>
2.3 controller class
<blockquote>@Controller
public class HelloWorldController {

//@RequestMapping(value = "/welcome") tells spring that this controller should process
//all all requests beginning with /welcome in the url path. That
//includes /welcome/* and /welcome.html
//it is the same as @RequestMapping("/welcome")
@RequestMapping(value = "/welcome")
public ModelAndView helloWorld() {

String message= "spring mvc hello world";
return new ModelAndView("welcome","message",message);
}

@RequestMapping(value="/student", method=RequestMethod.GET)
public ModelAndView student() {

return new ModelAndView("student","command", new Student());
}

@RequestMapping(value="/addStudent", method=RequestMethod.POST)
@ExceptionHandler({SpringException.class})
public String addStudent(@ModelAttribute("springweb")Student stu,ModelMap model) {

if(stu.getName().length()&lt;2) {
throw new SpringException("name is too less");
}
model.addAttribute("name", stu.getName());
model.addAttribute("age", stu.getAge());
model.addAttribute("id", stu.getId());

return "result";
}

}</blockquote>
2.4 view (jsp page)
<blockquote>&lt;%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%&gt;
&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"&gt;
&lt;title&gt;Insert title here&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
${message}
&lt;/body&gt;
&lt;/html&gt;</blockquote>
2.5 custom exception
<ul>
	<li>define an exception which extending RuntimeException</li>
	<li>state exception and corresponding error view in spring mvc config xml file like 2.2 above</li>
	<li>throw this exception in controller class</li>
	<li>create error view</li>
</ul>
