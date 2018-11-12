---
layout : post
title : Apache Shiro Hello World
comments: true
categories : Java
---

Apache Shiro  is a powerful and easy-to-use Java security framework that performs authentication, authorization, 
cryptography, and session management.

This example is a hello world web application which in spring, struts and mybatis.

### Maven Pom

  Add `shiro-core`, `shiro-web` and `shiro-spring` in pom file.
  
### web.xml

```xml
<filter>
  	<filter-name>shiroFilter</filter-name>
  	<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
  	<init-param>
  		  <param-name>targetFilterLifecycle</param-name>
  		  <param-value>true</param-value>
  	</init-param>
</filter>
<!-- other filter -->
<filter-mapping>
  	<filter-name>shiroFilter</filter-name>
  	<url-pattern>/*</url-pattern>
  	<dispatcher>REQUEST</dispatcher>
    <dispatcher>FORWARD</dispatcher>
    <dispatcher>INCLUDE</dispatcher>
    <dispatcher>ERROR</dispatcher>
</filter-mapping>
<!-- other filte mapping -->
```

**Note that shiro filter must be the first filter**

### Spring config

```xml
<!-- shiro config start -->
	<bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
  
		<property name="securityManager" ref="securityManager" />
		
    	<property name="loginUrl" value="/login.html" />
    	
    	<property name="filterChainDefinitions">
    		<value>
    	            /login.html = anon
    		    /login.action = anon
    		    /**/*.css = anon
    		    /**/*.js = anon
    		    /logout = logout
                   /** = authc
    		</value>
    	</property>
	</bean>
	
	<bean id="logout" class="org.apache.shiro.web.filter.authc.LogoutFilter"> 
        <property name="redirectUrl" value="/login.html" /> 
        
    </bean>
	
	<bean name="myRealm" class="example.test2.service.RealmService" >
	     
	     <property name="credentialsMatcher" ref="passwordMatcher" />
    </bean>
	
	
	<bean id="sessionManager" class="org.apache.shiro.web.session.mgt.DefaultWebSessionManager">
    	<!--  <property name="globalSessionTimeout" value="60000" /> -->
    	
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
    <!-- shiro config end -->
```

  **Note: in order to use MD5 , hexFormat has to be used**

#### Filter Chian Urls

  The format of each line in the `filterChainDefinitions` section is as follows:
  
  `_URL_Ant_Path_Expression_ = _Path_Specific_Filter_Chain`
  
  For example:
  
  ```
  /admin/** = authc, roles[administrator]
  /rest/** = authc, rest
  /remoting/rpc/** = authc, perms["remote:invoke"]
  ```
  
  The token on the left of the equals sign (=) is an **Ant-style path expression** 
  relative to your web application’s context root.
  
  `/account/** = ssl, authc` means that any request to my application’s path of `/account` or 
  any of it’s sub paths (`/account/foo`, `/account/bar/baz`, etc) will trigger the ‘ssl, authc’ filter chain.
  
  Note that all path expressions are relative to your application’s context root. 
  
  This means that if you deploy your application one day to, say, `www.somehost.com/myapp` and then later 
  deploy it to `www.anotherhost.com` (no ‘myapp’ sub-path), the pattern matching will still work. 
  
  All paths are relative to the `HttpServletRequest.getContextPath()` value.

##### Ant-style path expression

  1. ? matches one character
  2. * matches zero or more characters
  3. ** matches zero or more directories in a path
  
  Examples :
  
  - com/t?st.jsp — matches com/test.jsp but also com/tast.jsp or com/txst.jsp
  - com/*.jsp — matches all .jsp files in the com directory
  - com/**/test.jsp — matches all test.jsp files underneath the com path, e.g. com/test.jsp, com/xx/test.jsp
  - org/springframework/**/*.jsp — matches all .jsp files underneath the org/springframework path
  - org/**/servlet/bla.jsp — matches org/springframework/servlet/bla.jsp but also org/springframework/testing/servlet/bla.jsp and org/servlet/bla.jsp
  - com/{filename:\\w+}.jsp will match com/test.jsp and assign the value test to the filename variable
  
Note: a pattern and a path must both be absolute or must both be relative in order for the two to match. 

      Therefore it is recommended that users of this implementation to sanitize patterns in order to prefix them 
      with "/" as it makes sense in the context in which they're used.

##### Order Matters

  URL path expressions are evaluated against an incoming request in the order they are defined and the **FIRST MATCH WINS**. 
  For example, let's asume that there are the following chain definitions:
  
  ```xml
  /account/** = ssl, authc
  /account/signup = anon
  ```
  
  If an incoming request is intended to reach `/account/signup/index.html` (accessible by all 'anon'ymous users), 
  it will never be handled!. The reason is that the `/account/**` pattern matched the incoming request first 
  and 'short-circuited' all remaining definitions.

  Always remember to define your filter chains based on a FIRST MATCH WINS policy!
  
### Realm Service

  Developr can customize the authorization logic by extending `AuthorizingRealm`.

```java
public class RealmService extends AuthorizingRealm{
	
	@Autowired
	private UserDao userDao;

	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
		
		Subject currentUser = SecurityUtils.getSubject();
		User user = (User) currentUser.getSession().getAttribute("user");
		if(user != null) {
			SimpleAuthorizationInfo authorizationInfo = new SimpleAuthorizationInfo();
			List<Permission> permissions = user.getPermissions();
			for(Permission p : permissions) {
				authorizationInfo.addStringPermission(p.getName());
			}
			return authorizationInfo;
		}
		
		return null;
	}

	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
		
		UsernamePasswordToken upToken = (UsernamePasswordToken) token;
		String userName = upToken.getUsername();
		String pwd = String.valueOf(upToken.getPassword());
		String encryptedPwd = new Md5Hash(pwd).toString();
		User user = new User();
		user.setUsername(userName);
		user.setPassword(encryptedPwd);
		
		JSONObject param = new JSONObject();
		param.put("username", userName);
		param.put("password", encryptedPwd);
		User dbUser = this.userDao.getUser(param);
		
		if (dbUser != null) {
			
			Subject currentUser = SecurityUtils.getSubject();
			currentUser.getSession().setAttribute("user", dbUser);
			return new SimpleAuthenticationInfo(userName, dbUser.getPassword().toCharArray(), getName());
		}

		return null;
	}

}
```

### Struts login action

```java
public String login() {
		
		Subject currentUser = SecurityUtils.getSubject();
		
		UsernamePasswordToken token = new UsernamePasswordToken(user.getUsername(), user.getPassword());
		
		try {
    
			currentUser.login(token);
			
		}catch(AuthenticationException e) {
			return "error";
		}
		
		return "success";
	}
```

  When `currentUser.login(token)` is called, `RealmService.doGetAuthenticationInfo()` will be executed.
