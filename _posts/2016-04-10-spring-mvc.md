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
```
<display-name>springMVCExample</display-name>
<welcome-file-list>
<welcome-file>index.jsp</welcome-file>
</welcome-file-list>

<servlet>
<!-- Once DispatcherServlet is initialized, it will look for file
<servlet-name>-servlet.xml in WEB-INF folder of web application
-->
<servlet-name>helloworld</servlet-name>
<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
<load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
<servlet-name>helloworld</servlet-name>
<!-- this servlet will handle all http request -->
<url-pattern>/</url-pattern>
</servlet-mapping>
```
2.2 spring mvc config xml
```
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:mvc="http://www.springframework.org/schema/mvc"
xmlns:context="http://www.springframework.org/schema/context"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd">

<context:component-scan base-package="cn.example.spring.mvc.controller" />

<bean id="viewResolver" class="org.springframework.web.servlet.view.UrlBasedViewResolver">
<property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
<property name="prefix" value="/WEB-INF/jsp/" />
<property name="suffix" value=".jsp" />
</bean>

<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">

<!-- this property means if there is SpringException thrown, then ExceptionPage.jsp will be called
<property name="exceptionMappings">
<props>
<prop key="cn.example.spring.mvc.exception.SpringException">
ExceptionPage
</prop>
</props>
</property> -->
<!-- next line means the defautl exception page is error.jsp -->
<property name="defaultErrorView" value="error" />
</bean>

</beans>
```
2.3 controller class
```
@Controller
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

if(stu.getName().length()<2) {
throw new SpringException("name is too less");
}
model.addAttribute("name", stu.getName());
model.addAttribute("age", stu.getAge());
model.addAttribute("id", stu.getId());

return "result";
}

}
```
2.4 view (jsp page)
```
<%@ page language="java" contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>

<title>Insert title here</title>
</head>
<body>
${message}
</body>
</html>
```
2.5 custom exception

- define an exception which extending RuntimeException
- state exception and corresponding error view in spring mvc config xml file like 2.2 above
- throw this exception in controller class
- create error view

