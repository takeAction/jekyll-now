---
layout : post
title : Share Session In Multiple Web Project
comments: true
categories : Apache Shiro
---

Suppose there are two web projects: `test4` and `test5`, the login page is in `test4` and when user access resources of `test5`, he/she will
be redirected to login page of `test4` if he/she has not login yet.

Therefore, the session should be shared between `test4` and `test5`.

There are some workarounds to implement this goal, e.g. `spring session`, `apache shiro`, this post is about `apache shiro`.

### Custom Session DAO

In order to share session, we need to write a custom session dao which extends `AbstractSessionDAO`.

Keep/read the session info in `redis`.

```java
public class ShiroRedisSessionDao extends AbstractSessionDAO {

	private long expireTime = 120000;

	@Autowired
	private RedisTemplate redisTemplate;

	public ShiroRedisSessionDao() {

		super();
	}

	public ShiroRedisSessionDao(long expireTime, RedisTemplate redisTemplate) {

		super();
		this.expireTime = expireTime;
		this.redisTemplate = redisTemplate;
	}

	@Override
	public void update(Session session) throws UnknownSessionException {

		if (session == null || session.getId() == null) {
			return;
		}
		session.setTimeout(expireTime);
		redisTemplate.opsForValue().set(session.getId(), session, expireTime, TimeUnit.MILLISECONDS);

	}

	@Override
	public void delete(Session session) {

		if (null == session) {
			return;
		}
		redisTemplate.opsForValue().getOperations().delete(session.getId());

	}

	@Override
	public Collection<Session> getActiveSessions() {

		return redisTemplate.keys("*");

	}

	@Override
	protected Serializable doCreate(Session session) {

		Serializable sessionId = this.generateSessionId(session);
		this.assignSessionId(session, sessionId);

		redisTemplate.opsForValue().set(session.getId(), session, expireTime, TimeUnit.MILLISECONDS);
		return sessionId;

	}

	@Override
	protected Session doReadSession(Serializable sessionId) {

		if (sessionId == null) {
			return null;
		}
		return (Session) redisTemplate.opsForValue().get(sessionId);

	}

	//ommit setters/gettters

}
```

### Custom Realm Service

You can implements a own realm service by extends `AuthorizingRealm`, please refer to previous post for detail.

**Note that if your want to store POJO in redis, it has to be serializable.  In addition, multiple web projects have to use same 
model, e.g. com.example.model.User.**

### Shiro config

Add custom session dao and cookie to session manager.

The shiro config in `test4` looks like next:
```xml
<bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
		<property name="securityManager" ref="securityManager" />
		
    	<property name="loginUrl" value="/login.html" />
    	<!-- it has to be /logout = logout, cannot be /test4/logout = logout -->
    	<property name="filterChainDefinitions">
    		<value>    			
    			/login.action = anon
    			/*.css = anon
    			/*.js = anon
    			/logout = logout
          /** = authc
    		</value>
    	</property>
	</bean>
	
	<bean id="logout" class="org.apache.shiro.web.filter.authc.LogoutFilter"> 
        <property name="redirectUrl" value="/login.html" /> 
        
    </bean>
	
	<bean name="myRealm" class="example.test4.service.RealmService" >
	     
	     <property name="credentialsMatcher" ref="passwordMatcher" />
    </bean>
	
	<bean id="customSessionDao" class="example.test4.dao.ShiroRedisSessionDao" />
	
	<bean id="simpleCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
        <constructor-arg name="name" value="shiro.sesssion"/>
        <property name="path" value="/"/>
</bean>
	
	
	<bean id="sessionManager" class="org.apache.shiro.web.session.mgt.DefaultWebSessionManager">
    	
    	
        <property name="sessionValidationSchedulerEnabled" value="true" /> 
     
        <property name="globalSessionTimeout" value="1800000" />
        <property name="sessionDAO" ref="customSessionDao" /> 
        <property name="sessionIdCookie" ref="simpleCookie"/> 
        <!-- set false to remove jsessionid = xxx at the end of url -->
        <property name="sessionIdUrlRewritingEnabled" value="false"/>
        
    </bean>
    
	<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
    	<property name="realm" ref="myRealm" />
    	<property name="sessionManager" ref="sessionManager" />
    </bean>
    
    <bean id="hashService" class="org.apache.shiro.crypto.hash.DefaultHashService">
    	<property name="hashAlgorithmName" value="MD5" />
    	<property name="hashIterations" value="1" />
    	<property name="generatePublicSalt" value="false" />
    </bean>
    
    <bean id="hexFormat" class="org.apache.shiro.crypto.hash.format.HexFormat">
    	
    </bean>
    
     <bean id="passwordService" class="org.apache.shiro.authc.credential.DefaultPasswordService">
    	<property name="hashService" ref="hashService" />
    	<property name="hashFormat" ref="hexFormat" />
    </bean>
    
    <bean id="passwordMatcher" class="org.apache.shiro.authc.credential.PasswordMatcher">
    	<property name="passwordService" ref="passwordService" />
    </bean>
    
    <bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor"/>
```

While the config in `test5` is:

```xml
<bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
		<property name="securityManager" ref="securityManager" />
		<!-- has to be http://192.168.88.6:8080/test4/login.html, cannot be 192.168.88.6:8080/test4/login.html,otherwise the browser address bar will changed to http://localhost:8080/test5/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/192.168.88.6:8080/test4/login.html -->
    	<property name="loginUrl" value="http://192.168.88.6:8080/test4/login.html" />
    	
    	<property name="filterChainDefinitions">
    		<value>
    			/test4/login.html = anon
    			/login.action = anon
    			/*.css = anon
    			/*.js = anon
    			/logout = logout
          /** = authc
    		</value>
    	</property>
	</bean>
  
  <bean id="logout" class="org.apache.shiro.web.filter.authc.LogoutFilter"> 
        <property name="redirectUrl" value="http://192.168.88.6:8080/test4/login.html" />         
  </bean>
    
  <bean id="simpleCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
        <constructor-arg name="name" value="shiro.sesssion"/>
        <property name="path" value="/"/>
</bean>

<!-- others are same as config of test4 -->
```

**Note: cookie path has to be /**
