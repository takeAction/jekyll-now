---
layout : post
title : Spring get value in runtime
categories : Spring
---

In spring, there are two approaches to get value in runtime:
- Property placeholder
- Spring Expression Language(EL)

### Property placeholder

  1. create a property file, like `test.properties`
  
     ```
     jdbc.username=root
     ...
     ```
     
  2. define PropertySourcesPlaceholderConfigurer
  
     `<context:property-placeholder location="test.properties"/>` 
     
     or 
     
     `@PropertySource("classpath:test.properties")`
     
     With annotation, developer have to define 
     
     ```
     @Bean 
     public static PropertySourcesPlaceholderConfigurer placeholderConfigurer() { 
         return new PropertySourcesPlaceholderConfigurer(); 
     }
     ```
     
  3. get value by **${variable-name}**
  
     ```
     <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">

		     <property name="username" value="${jdbc.username}" />
		     ....
	   </bean>
     ```
     
     ```
     @Autowired
     private Environment env;
     ...
     dataSource.setUrl(env.getProperty("jdbc.username"));
     ```
     
 ### Spring EL
 
   Spring EL has to be defined in **#{spring-expression-language}**
   
   For example, `#{user.name}`, `#{'hello'}`, `#{usr.setName()}`, `#{user.getName()?.toUpperCase()}`
