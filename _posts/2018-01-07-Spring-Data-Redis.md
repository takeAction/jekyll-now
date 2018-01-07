---
layout: post
title : Spring Data Redis
categories : Redis
---

### Spring config

  With the help of spring, developer can config java client for redis, pool etc easily.
  
  In order to use spring data redis, following bean should be defined in spring config file, take Jedis for example:
  1. Pool config
  2. Connection factory
  3. RedisTemplate
  4. Cache manager, optional
  
```XML
<bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">
	<property name="maxIdle" value="300" />
	<property name="maxTotal" value="600" />
	<property name="maxWaitMillis" value="1000" />
	<property name="testOnBorrow" value="true" />
</bean>

<bean id="connectionFactory" 
class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory"
p:host-name="192.168.2.102" p:port="6379" p:password=""
p:pool-config-ref="poolConfig" />

<bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">
	<property name="connectionFactory" ref="connectionFactory" />
	<property name="keySerializer">
		<bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />
	</property>
	<property name="valueSerializer">
		<bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />
	</property>
</bean>

<!--  
   <bean id="redisCacheManager" class="org.springframework.data.redis.cache.RedisCacheManager">
       <constructor-arg name="redisOperations" ref="redisTemplate"/>
   </bean>
-->
```

#### RedisConnectionFactory

  > RedisConnection provides the building block for Redis communication as it handles the communication with 
  > the Redis back-end. It also automatically translates the underlying connecting library exceptions to Spring’s 
  > consistent DAO exception hierarchy so one can switch the connectors without any code changes 
  > as the operation semantics remain the same.
  > Active RedisConnection s are created through RedisConnectionFactory. 
  > In addition, the factories act as PersistenceExceptionTranslator s, meaning once declared, 
  > they allow one to do transparent exception translation. For example, exception translation through the use of 
  > the @Repository annotation and AOP. 

#### Serializer

  Because data is stored in redis as bytes, therefore our data need to be converted to bytes and vice versa.
  
  Spring Data Redis provides an interface called RedisSerializer<T> to do this job, some implementations as follows:
  
  - JacksonJsonRedisSerializer<T>, 
  - JdkSerializationRedisSerializer, 
  - OxmSerializer
  - StringRedisSerializer
  
  Redis is a key-value store structure, and in Java, key is string, value can be Object, List etc. 
  Hence, `StringRedisSerializer` is used to serialize/deserialize key, `JdkSerializationRedisSerializer` can be used 
  to process the value.
  
  Developer can define serializer strategy in java code besides in spring config file.
  
### Java code

```Java
  @SuppressWarnings("rawtypes")
  @Autowired
	private RedisTemplate redisTemplate;

	@SuppressWarnings("unchecked")
	@Override
	public void add(User user) {
		
		ValueOperations<String, User> valueops = redisTemplate.opsForValue();
		valueops.set(user.getId(), user);
	}

	@SuppressWarnings("unchecked")
	@Override
	public User getUser(String key) {
		
		ValueOperations<String, User> valueops = redisTemplate.opsForValue();
		User user = valueops.get(key);
		return user;
	}
```
