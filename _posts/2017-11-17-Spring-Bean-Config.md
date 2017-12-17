There are three approaches to config bean in spring:
1. Implict
2. Explict by java
3. Explict by xml

### Implict

- Create a config class, add `@Configuration` and `@ComponnetScan` for it,

```
        @Configuration
        @ComponentScan(basePackages="example.my.spring")
        public class CarConfig {

        }
```

By default, spring only scan the classes whose package is the same as config class'.
Alternatively, `<context:component-scan base-package="example.my.spring"></context:component-scan>` can be used in spring
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

- for web project, add this context file in web.xml

```XML
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:beans.xml</param-value>
</context-param>
```
