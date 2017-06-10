### Configure the spring listener and struts2 filter in web.xml

```
<context-param>

<param-name>contextConfigLocation</param-name>

<!-- the applicationContext.xml should be placed under WEN-INF by default. However we can use <context-param> to declare it is in src/main/resource with classpath:applicationContext.xml-->

<param-value>classpath:applicationContext.xml</param-value>

</context-param>

<listener>

<!-- Following listener is used to load the spring context file. -->

<listener-class>

org.springframework.web.context.ContextLoaderListener

</listener-class>

</listener>

<filter>

<filter-name>struts2</filter-name>

<!-- struts2 filter class path may be differ in different version-->

<filter-class>

org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter

</filter-class>

</filter>

<filter-mapping>

<filter-name>struts2</filter-name>

<url-pattern>/*</url-pattern>

</filter-mapping>
```

### define struts2 action bean, data source and mybatis sql session in applicationContext.xml

```
<bean id="helloworld" class="example.my.integration.struts.HelloWorld">

<!-- add sqlSession in action class in this case, because we will query db from action class directly with the help of sqlSession -->

<property name="sqlSession" ref="sqlSession" />

</bean>

<bean id="dataSource"

class="org.springframework.jndi.JndiObjectFactoryBean">

<!-- Using JndiObjectFactoryBean such that we can define db resource in tomcat context.xml without configuring in project. Meanwhile we don't need to use any code to do lookup jndi or connect db, just use mybatis sqlSession api to operate db-->

<property name="jndiName" value="java:/comp/env/jdbc/example" />

</bean>

<!-- integrate spring with mybatis by using following two beans -->

<bean id="sqlSessionFactory"

class="org.mybatis.spring.SqlSessionFactoryBean">

<property name="configLocation" value="classpath:mybatis-config.xml" />

<property name="dataSource" ref="dataSource" />

</bean>

<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">

<constructor-arg index="0" ref="sqlSessionFactory" />

</bean>
```

### In struts.xml, the value of action class should be bean id which declared in applicationContext.xml


### add struts2-spring-plugin, mybatis-spring jars in pom.xml
