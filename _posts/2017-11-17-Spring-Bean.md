---
layout : post
title : Spring Bean
categories : Spring
---

## Bean Life Cycle

  Spring defines following bean scopes:
  
  - Singleton
  - Prototype
  - Session
  - Request
  
  By default, spring bean is singleton.
  
#### Define scope
  
  `@Scope( ConfigurableBeanFactory. SCOPE_ PROTOTYPE)` or `@Scope("prototype")`, former is better, because it is safer and
  not easy be wrong.
  
  or `<bean id="..." class="..." scope="prototype" />`
  
  `Session` and `Request` scopes are in `WebApplicationContext`

## Config Bean

There are three approaches to config bean in spring:
1. Implict
2. Explict by java
3. Explict by xml

### Implict

- Create a config class, add `@Configuration` and `@ComponnetScan` for it,

```Java
@Configuration
@ComponentScan(basePackages={"packageA", "packageB"})
public class CarConfig {

}
```

**Component scan only scans @Component**

By default, spring only scan the classes whose package is the same as config class'.

Alternatively, `<context:component-scan base-package="packageA"></context:component-scan>` can be used in spring
context xml file.

- Create bean, add `@Component` for it

```Java
@Component("suv")
public class SUV implements Car {

	@Override
	public void run() {
		
		System.out.println("SUV is running!");
	}

}
```

#### @Component, @Controller, @Service and @Repository

- With these annotation, spring is able to import beans into the container so that developer don't have to define them
  explicitly in xml after enabling component scan. These annotations are called stereotype annotation as well.
  
- @Controller, @Service and @Repository are annotaed with @Component, e.g.

```
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Repository {
    ....
}
```

- @Component is a general purpose stereotype annotation, is equal to `<bean>` of xml, 
  while @Controller, @Service and @Repository are specialized annotation of @Component, they are equal @Component + some more
  special functionality
  
  By annotating class with @Controller, @Service or @Repository, your classes are more properly suited for processing by tools or 
  associating with aspects. For example, these stereotype annotations make ideal targets for pointcuts.
  
- @Repository is used for DAO layer, it provides additional benefits : makes the unchecked exceptions(thrown from DAO methods) 
  eligible for translation into spring `DataAccessException`
  
  And for this, we’re provided with PersistenceExceptionTranslationPostProcessor, that we are required to add in our Spring’s
  application context like this:

  `<bean class="org.springframework.dao.annotation.PersistenceExceptionTranslationPostProcessor"/>`
   This bean post processor adds an advisor to any bean that’s annotated with @Repository so that any platform-specific exceptions are  
   caught and then rethrown as one of Spring’s unchecked data access exceptions.
  
- @Service is uesed in service layer

- @Controller indicates this class is a controller, like spring web mvc controller class

**A good practice is using @Controller, @Service and @Repository at most of the time, @Component should be used when your class
does not fall into either of controller, service or DAO**

When component scan is declared, developer no longer need to declare context:annotation-config, because autowiring is implicitly
enabled in this case.

**Always use these annotations over concrete classes rather than interfaces**

### Explicit by java

Create a config class, add `@Configuration` for it, 
then define a method to return a bean instance and add `@Bean` for this method

```Java
@Configuration
public class CarConfig {
	
	@Bean
	public Car car() {
		
		return new SUV();
	}

}
```

```Java
public class SUV implements Car {

	
	@Override
	public void run() {
		
		System.out.println("explit java config suv");
	}

}
```

For above two methods, the web.xml is suppose to be

```Java
<context-param>
    <param-name>contextClass</param-name>
    <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
</context-param>
  
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>example.my.spring.config.CarConfig</param-value>
</context-param>
```

### Explicit by xml

- Define POJO
- Create a spring context xml file and define beans in it

```XML
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
    	http://www.springframework.org/schema/beans/spring-beans.xsd
    	http://www.springframework.org/schema/context
    	http://www.springframework.org/schema/context/spring-context.xsd
    	http://www.springframework.org/schema/aop  
		http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx  
		http://www.springframework.org/schema/tx/spring-tx.xsd">
		
	<context:component-scan base-package="example.my.model"></context:component-scan>
	<context:annotation-config/>
	<tx:annotation-driven />
	
	<bean id="service" class="example.my.service.MyService" >
    <property name="property-name-of-bean" ref="another-bean-id" />
    <property name="property-name-of-bean" value="property-value" />
  </bean>
```	

- the web.xml should be

```XML
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:beans.xml</param-value>
</context-param>
```
