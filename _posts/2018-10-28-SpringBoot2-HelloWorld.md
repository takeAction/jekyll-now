---
layout : post
title : SpringBoot2 Hello World
comments: true
categories : Spring
---

This example is base on spring boot 2, spring mvc, mybatis, mysql.

### Code

#### Project Structure

![_config.yml]({{ site.baseurl }}/images/sb-project-structure.png.PNG)

#### Mave Pom

```xml
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.6.RELEASE</version>
	</parent>

	<name>springboot Maven Webapp</name>
	<url>http://maven.apache.org</url>
	<dependencies>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.3.2</version>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>

	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
		<finalName>springboot</finalName>
	</build>
```

#### Application Yml

`src/main/resources/application.yml` looks like:

```yml
spring:
  datasource: 
    url: jdbc:mysql://localhost:3306/test
    username: root
    password: 
    # driver-class-name can be omitted because spring boot can detect it by url
    # driver-class-name: com.mysql.jdbc.Driver

mybatis:
  type-aliases-package: example.springboot.model
  mapper-locations: classpath:/sql/*.xml
  configuration:
    map-underscore-to-camel-case: true        
        
logging:
  level:
    root: info
```

#### Mybatis Sql Xml file

```xml
<mapper namespace="example.springboot.dao.UserDao">
	
	<select id="getUser" resultType="user">
		SELECT
			*
		FROM
			user
		WHERE
			user_id = #{id}
	</select>
</mapper>
```

#### Class

```java
@SpringBootApplication
@MapperScan("example.springboot.dao")
public class Example {	
	
	public static void main(String[] args) throws Exception {
		
		SpringApplication.run(Example.class, args);	
		
	}

}

@RestController
public class MyController {
	
	@Autowired
	private UserService userService;
	
	@RequestMapping("/")
	String home() {
		User user = this.userService.getUser(1);
		return "Spring Boot Hello World : "+user.getUserName();
	}

}

public interface UserDao {
	
	User getUser(Integer userId);

}

public class User implements Serializable{	
	
	private static final long serialVersionUID = -7561411960266528475L;

	private Integer userId;
	
	private String userName;

	public Integer getUserId() {
		return userId;
	}

	public void setUserId(Integer userId) {
		this.userId = userId;
	}

	public String getUserName() {
		return userName;
	}

	public void setUserName(String userName) {
		this.userName = userName;
	}

}

public interface UserService {
	
	User getUser(Integer userId);

}

@Service
public class UserServiceImpl implements UserService{
	
	@Autowired
	private UserDao userDao;

	@Override
	public User getUser(Integer userId) {
		
		return this.userDao.getUser(userId);
	}

}
```

### @SpringBootApplication

> The @SpringBootApplication annotation is often placed on your main class, and it implicitly defines a base
> search package for certain items. 
> For example, if you are writing a JPA application, the package of the @SpringBootApplication annotated class is used to
> search for @Entity items. Using a root package also allows component scan to apply only on your project.
> If you don't want to use @SpringBootApplication, the @EnableAutoConfiguration and @ComponentScan annotations that
> it imports defines that behaviour so you can also use that instead.

### @EnableAutoConfiguration

> This annotation tells Spring Boot to guess how you want to configure Spring, 
> based on the jar dependencies that you have added. Since spring-boot-starter-web added Tomcat and Spring MVC,
> the auto-configuration assumes that you are developing a web application and sets up Spring accordingly.
> 
> Auto-configuration is designed to work well with Starters, but the two concepts are not directly tied. 
> You are free to pick and choose jar dependencies outside of the starters.
> Spring Boot still does its best to auto-configure your application.

### @MapperScan

  By default, mybatis will register the classes as mapper which annotated with `@Mapper` by searching the package
  which is the package of main class. In this example, it will search package 'example.springboot'.
  
  However, we can use `@MapperScan` to indicate mybatis where to search mapper class even these mapper has not been
  annotated with `@Mapper'.
  
### @RestController & @RequestMapping

> They are Spring MVC annotations. (They are not specific to Spring Boot.) 
> 
> It is known as a stereotype annotation. 
> It provides hints for people reading the code and for Spring that the class plays a specific role.
> In this case, our class is a web @Controller, so Spring considers it when handling incoming web requests.
> @RestController annotation tells Spring to render the resulting string directly back to the caller.
> 
> The @RequestMapping annotation provides routing information. 
> It tells Spring that any HTTP request with the / path should be mapped to the home method.

### SpringApplication.run

> SpringApplication bootstraps our application, starting Spring, which, in turn, 
> starts the auto-configured Tomcat web server.
> We need to pass Example.class as an argument to the run method to tell SpringApplication 
> which is the primary Spring component.

### spring-boot-starter-parent

> Maven users can inherit from the spring-boot-starter-parent project 
> to obtain sensible defaults. The parent project provides the following features: 
> Java 1.8 as the default compiler level. UTF-8 source encoding. A Dependency 
> Management section, inherited from the spring-boot-dependencies pom, that 
> manages the versions of common dependencies. This dependency management lets 
> you omit <version> tags for those dependencies when used in your own pom. 
> Sensible resource filtering. Sensible plugin configuration (exec plugin, 
> Git commit ID, and shade). Sensible resource filtering for application.properties 
> and application.yml including profile-specific files (for example, application-dev.properties 
> and application-dev.yml) Note that, since the application.properties and 
> application.yml files accept Spring style placeholders (${…​}), the Maven 
> filtering is changed to use @..@ placeholders. (You can override that by 
> setting a Maven property called resource.delimiter.)
> 
> You should need to specify only the Spring Boot version number on this dependency. 
> If you import additional starters, you can safely omit the version number
> 
> With that setup, you can also override individual dependencies by overriding a property in your own project. 
> For instance, to upgrade to another Spring Data release train, you would add the following to your pom.xml: 
> <properties> 
> <spring-data-releasetrain.version>Fowler-SR2</spring-data-releasetrain.version> 
> </properties> 
> Check the https://github.com/spring-projects/spring-boot/blob/v2.0.6.RELEASE/spring-boot-project/spring-boot-dependencies/pom.xml 
> for a list of supported properties.

### spring-boot-starter-web

> Starters are a set of convenient dependency descriptors that you can include in your application. 
> You get a one-stop shop for all the Spring and related technologies that you need without having to 
> hunt through sample code and copy-paste loads of dependency descriptors. 
> For example, if you want to get started using Spring and JPA for database access, 
> include the spring-boot-starter-data-jpa dependency in your project. 
> The starters contain a lot of the dependencies that you need to get a project up and running quickly 
> and with a consistent, supported set of managed transitive dependencies.

### mybatis-spring-boot-starter

name of non spring offical starter looks like `thirparty_name-spring-boot-starter`

### spring-boot-devtools

> It provide additional development-time features such as automatic restart 
> Developer tools are automatically disabled when running a fully packaged application. 
> If your application is launched from java -jar or if it is started from a special classloader, 
> then it is considered a “production application”. Flagging the dependency as optional in Maven or 
> using a custom`developmentOnly` configuration in Gradle (as shown above) is a best practice that 
> prevents devtools from being transitively applied to other modules that use your project.

### spring-boot-maven-plugin

> Spring Boot includes a Maven plugin that can package the project as an executable jar.
> If you use the Spring Boot starter parent pom, you need to add only the plugin. 
> There is no need to configure it unless you want to change the settings defined in the parent.
