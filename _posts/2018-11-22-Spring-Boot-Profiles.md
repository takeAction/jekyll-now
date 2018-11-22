---
layout : post
title : Spring Boot Profiles
comments: true
categories : Spring
---

### Profiles

  One application has some environments, e.g. dev/test/uat/prod evironment.
  
  Each environment requires a setting that is specific to it. For example, in dev, the log level should be debug,
  while in prod, it should be error.
  
  These environments host specific configurations called Profiles.
  
### Maintain Profiles

#### Create Profiles

  Multiple properties/yml configuration files should be created base on environment.
  
  Their name is in the format `application-{profile}.yml`, such as `application-dev.yml`.
  
  Spring Boot will automatically load the properties from `application.yml` file for all profiles, 
  and the ones in profile-specific files only for the specified profile, that is profile-specific will override the `application.yml`.
  
  For example, the `application.yml` looks like:
  
  ```yml
  logging.level:
    root: info
  ```
  
  While `application-dev.yml` is:
  
  ```yml
  logging.level:
    root: info
    java.sql.PreparedStatement: debug
    com.my.rest.dao: debug
  ```
  
#### Active Profile

  There are 3 approaches to active specific profile:
  
  1. add `spring.profiles.active=dev` in `application.yml`
  2. active profile programmatically with the help of `SpringApplication.setAdditionalProfiles("dev");`
  3. set profiles using Maven, we can specify profile names under `spring-boot-maven-plugin` in `pom.xml`:
  
     ```xml
     <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
            <profiles>
                <profile>dev</profile>
            </profiles>
        </configuration>
     </plugin>
     ```
     
     then right click this spring boot project -> run as -> maven build -> input `spring-boot:run` as goal.
     
     In this solution, if you right click this spring boot project -> run as -> spring boot app, the specified profile will not be actived.
     
### References

  [baeldung](https://www.baeldung.com/spring-profiles)
