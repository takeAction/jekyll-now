---
layout : post
title : Spring Cache With Redis In Spring Boot
comments: true
categories : Spring
---

Spring provides a cache abstraction which user can add/change concerete cache implementation easily.

It can be used on top of methods.

### Spring cache annotation

#### @EnableCaching

  Enables the Spring caching support.
  
#### @CacheConfig

  It provides a mechanism for sharing common cache-relatedsettings at the class level, such as `cacheNames`.
  
#### @Cacheable

  Indicating that the result of invoking a method (or all methodsin a class) can be cached. 

  Each time an advised method is invoked, it will check whether the method has been already invoked for the given key.
  
  Method parameters are used as the key by default, but a SpEL expression can be provided via the key attribute
  or a custom `org.springframework.cache.interceptor.KeyGenerator` implementation canreplace the default one. 
  
  If there is value in cache, then the data is fectched from cache, otherwise it is fetched from DB and stored in cahche.

#### @CachePut

  It always causes the method to be invoked and **its result** to be stored in the associated cache.
  
#### @CacheEvict

  Remove cache if the data is deleted.
  
### Code

#### Maven Dependencies

```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-cache</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-redis</artifactId>
		</dependency>
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
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
```

#### Application Yml

```yml
spring:
  datasource: 
    url: jdbc:mysql://localhost:3306/test
    username: root
    password: 123456
  cache:
    redis:
      # disable remove data in redis automatically
      time-to-live: -1
  redis:
    database: 3
    host: 192.168.88.25
    port: 6379
    password:
    # milliseconds, if set to 0, then error RedisCommandTimeoutException: Command timed out after no timeout thrown
    timeout: 120000
    jedis:    
      pool:
        max-active: 8
        max-idle: 8
        min-idle: 0
        max-wait: -1
    

mybatis:
  type-aliases-package: com.example.sb.model
  mapper-locations: classpath:sql/*.xml
  configuration:
    map-underscore-to-camel-case: true        
        
logging.level:
  root: info
  java.sql.PreparedStatement: debug
  com.example.sb.dao: debug
```

#### Cache Config Class

```java
@Configuration
@EnableCaching
public class CacheConfig {

}
```

#### Service Class

```java
@Service
@CacheConfig(cacheNames = "user")
public class UserServiceImpl implements UserService{
	
	@Autowired
	private UserDao userDao;

	@Override
	@Cacheable
	public User getUser(Integer userId) {
		
		return this.userDao.getUser(userId);
	}

	/**
	 * cacheput will update cache according to the result of method execution
	 * so update method has to return value, otherwise spring will update the cache value to null
	 */
	@Override
	@CachePut(key = "#user.userId")
	public User updateUser(User user) {
		
		this.userDao.updateUser(user);
		
		return user;
	}

	@Override
	@CacheEvict
	public void deleteById(Integer userId) {
		
		this.userDao.deleteById(userId);
	}

}
```

### Mybatis 2nd Level Cache

  User also can use mybatis second level cache with redis. Please refer [it](http://www.mybatis.org/mybatis-3/sqlmap-xml.html#cache) for detail.
  
  Mybatis 2nd level cache is able to cache the select query result automatically as well as updating cache when data is updated.
  
  User can implements `org.apache.ibatis.cache.Cache` to create 2nd level cache.
