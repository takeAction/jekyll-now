---
layout : post
title : Spring Security
comments: true
categories : [Spring]
---

This post illustrates spring security under Spring Boot 2.1.4 with gradle 3.

Spring security can be done by java config or xml config.

This example is created by spring tool suite.

### Dependencies

```gradle
plugins {
	id 'org.springframework.boot' version '2.1.4.RELEASE'
	id 'java'
	id 'war'
}

apply plugin: 'io.spring.dependency-management'

group = 'example.spring.security'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-security'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	runtimeOnly 'org.springframework.boot:spring-boot-devtools'
	providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'org.springframework.security:spring-security-test'
}
```

### Front-end pages

These `html` fileds placed in `src/main/resources/templates`.

`hello.html`:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Hello World!</title>
    </head>
    <body>
        <h1 th:inline="text">Hello [[${#httpServletRequest.remoteUser}]]!</h1>
        <form th:action="@{/logout}" method="post">
            <input type="submit" value="Sign Out"/>
        </form>
    </body>
</html>
```

`login.html`:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Spring Security Example </title>
    </head>
    <body>
        <div th:if="${param.error}">
            Invalid username and password.
        </div>
        <div th:if="${param.logout}">
            You have been logged out.
        </div>
        <form th:action="@{/login}" method="post">
            <div><label>  Name : <input type="text" name="username"/> </label></div>
            <div><label> Password: <input type="password" name="password"/> </label></div>
            <div><input type="submit" value="Sign In"/></div>
        </form>
    </body>
</html>
```

`home.html`:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org" xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
    <head>
        <title>Spring Security Example</title>
    </head>
    <body>
        <h1>Welcome!</h1>

        <p>Click <a th:href="@{/hello}">here</a> to see a greeting.</p>
    </body>
</html>
```

### Controller

```java
@Controller
public class MyController {	
	
	@RequestMapping(value= {"/", "/home"})
	public String home() {
		
		return "home";
	}
	
	@RequestMapping("/hello")
	public String hello() {
		
		return "hello";
	}
	
	@RequestMapping("/login")
	public String login() {
		
		return "login";
	}

}
```

### Java Config

In order to implement spring security by java config, developer need to define a custom class which extends
`WebSecurityConfigurerAdapter`.

In this custom class, define security logic and how to compare crendentials.

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
	
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/", "/home").permitAll()
                .anyRequest().authenticated()
                .and()
            .formLogin()
                .loginPage("/login")
                .permitAll()
                .and()
            .logout()
                .permitAll();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) 
      throws Exception {
    	
        PasswordEncoder encoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
        
        auth.inMemoryAuthentication()
          .withUser("spring")
          .password(encoder.encode("secret"))
          .roles("USER");
    }
}
```

### Xml Config

Define security logic in a xml file, it is under `src/main/resources`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/security" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:beans="http://www.springframework.org/schema/beans"
    xsi:schemaLocation="
		http://www.springframework.org/schema/security 
        http://www.springframework.org/schema/security/spring-security.xsd
		http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd"
>

<http >
  	 
    <intercept-url pattern="/" access="permitAll" />
    <intercept-url pattern="/home" access="isAnonymous()" />
    <intercept-url pattern="/login*" access="isAnonymous()" />
    <intercept-url pattern="/**" access="isAuthenticated()"/>
    <!--  
    <intercept-url pattern="/dba**" access="hasRole('ADMIN') and hasRole('DBA')" />
    -->
    <form-login login-page='/login'
      default-target-url="/hello"
      authentication-failure-url="/login?error=true" />
    <logout logout-success-url="/login" />
</http>
 
<authentication-manager>
    <authentication-provider>
        <user-service>
            <user name="user1" password="$2a$10$7do6DN1dYhUcjselGB5RweOB1KbDGv.YcSfMgRpxyykX8u8/GQzym" authorities="ROLE_USER" />
            <!-- 
            <user name="dba"   password="root123" authorities="ROLE_ADMIN,ROLE_DBA" />
             -->
        </user-service>
          <password-encoder ref="encoder" />  
    </authentication-provider>
</authentication-manager>
 
<beans:bean id="encoder"
  class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder">
</beans:bean>

</beans:beans>
```

This password is encoded by `BCryptPasswordEncoder`, in this example, password is hard code in xml file with this encoder
for the sake of simplicity.

Then import this xml file in spring boot application :

```java
@SpringBootApplication
@EnableWebSecurity
@ImportResource("classpath:spring-security.xml")
public class SpringSecurityXmlApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringSecurityXmlApplication.class, args);
	}

}
```

**Note: `@EnableWebSecurity` has to be added to disable the default security configuration.**

#### permitAll vs isAnonymous

`permitAll` means everyone can access it, while `isAnonymous` represents only anonymous users can access it, login user cannot.

#### Custom authentication

There are two ways to achieve custom authentication:

1. Implements `UserDetailsService`
2. Implements `AuthenticationProvider`

```java
@Service
public class MyUserDetailService implements UserDetailsService{

	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		
		//get user by username from db then check
		if( username.equals("user1") ) {
			
			return User.withUsername("user1")
					.password("$2a$10$7do6DN1dYhUcjselGB5RweOB1KbDGv.YcSfMgRpxyykX8u8/GQzym")
					.roles("test")
					.build();
		}else {
			throw new UsernameNotFoundException("user not found");
		}
		
	}

}
```

#### UserDetailsService vs AuthenticationProvider

Please refer to [difference](https://stackoverflow.com/questions/31630818/spring-security-custom-authentication-authenticationprovider-vs-userdetailsser)

### Reference

[spring-boot-security-autoconfiguration](https://www.baeldung.com/spring-boot-security-autoconfiguration)

[spring-security-authentication](https://dzone.com/articles/spring-security-authentication)
