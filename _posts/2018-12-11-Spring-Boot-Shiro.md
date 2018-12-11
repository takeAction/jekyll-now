---
layout : post
title : Spring Boot Shiro
comments: true
categories : Spring
---

Integrate Apache Shiro into spring boot 2.

### Add dependency

  Add `shiro-spring-boot-web-starter` to maven pom:
  
```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional>
		</dependency>

		<dependency>
			<groupId>org.apache.shiro</groupId>
			<artifactId>shiro-spring-boot-web-starter</artifactId>
			<version>1.4.0</version>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-freemarker</artifactId>
		</dependency>
```

### Shiro in yaml

```yml
shiro:
  loginUrl: /login
  successUrl: /secure
  unauthorizedUrl: /login
  sessionManager:
    sessionIdUrlRewritingEnabled: false
```

### Custom Realm

```java
public class MyCustomRealm extends AuthorizingRealm {
	
	private Map<String, String> credentials = new HashMap<>();
	private Map<String, Set<String>> roles = new HashMap<>();
	private Map<String, Set<String>> perm = new HashMap<>();
	 
	{
	    credentials.put("user", "password");
	    credentials.put("user2", "password2");
	    credentials.put("user3", "password3");
	                                           
	    roles.put("user", new HashSet<>(Arrays.asList("admin")));
	    roles.put("user2", new HashSet<>(Arrays.asList("editor")));
	    roles.put("user3", new HashSet<>(Arrays.asList("author")));
	                                                              
	    perm.put("admin", new HashSet<>(Arrays.asList("*")));
	    perm.put("editor", new HashSet<>(Arrays.asList("articles:*")));
	    perm.put("author", 
	      new HashSet<>(Arrays.asList("articles:compose", 
	      "articles:save")));
	}

	@Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
		
        Set<String> roleNames = new HashSet<>();
        Set<String> permissions = new HashSet<>();

        String userName = (String) principals.getPrimaryPrincipal();
        Set<String> userRoles = roles.get(userName);
        roleNames.addAll(userRoles);
        
        Iterator<String> ite = userRoles.iterator();
        while(ite.hasNext()) {
        	String permissionId = ite.next();
        	permissions.addAll(perm.get(permissionId));
        }
        
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo(roleNames);
        info.setStringPermissions(permissions);
        return info;
    }


	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
		
		UsernamePasswordToken uToken = (UsernamePasswordToken) token;
        
	    if(uToken.getUsername() == null
	      || uToken.getUsername().isEmpty()
	      || !credentials.containsKey(uToken.getUsername())) {
	          throw new UnknownAccountException("username not found!");
	    }
	                                         
	    return new SimpleAuthenticationInfo(
	      uToken.getUsername(), 
	      credentials.get(uToken.getUsername()), 
	      getName()); 
	}

}
```

### Boot application

```java
@SpringBootApplication
public class SecurityApplication {

	public static void main(String[] args) {
		SpringApplication.run(SecurityApplication.class, args);
	}
	
	//If we did not define the Realm bean, ShiroAutoConfiguration will, by default, 
	//provide an IniRealm implementation that expects to find a shiro.ini file in src/main/resources 
	//or src/main/resources/META-INF.
	@Bean
	public Realm realm() {
	    return (Realm) new MyCustomRealm();
	}
	
	//If we do not define a ShiroFilterChainDefinition bean, the framework secures all paths and sets the login URL as login.jsp.
	@Bean
	public ShiroFilterChainDefinition shiroFilterChainDefinition() {
	    DefaultShiroFilterChainDefinition filter
	      = new DefaultShiroFilterChainDefinition();
	    //order matters
	    filter.addPathDefinition("/", "anon");
	    filter.addPathDefinition("/login", "anon");
	    filter.addPathDefinition("/**", "authc");
	 
	    return filter;
	}
  }
```

### View page

index.ftl:

```jsp
<html>
<head>
    <title>Index</title>
</head>
<body>
    <h1>Welcome Guest!</h1>
    <br>
    <a href="login">Login</a>
</body>
</html>
```

secure.ftl:

```jsp
<html>
<head>
    <title>Secure</title>
</head>
<body style="margin-left: 30px;">
<h1>Welcome ${username}!</h1>
<p><strong>Role</strong>: ${role}</p>
<p><strong>Permissions</strong></p>
<p>${permission}</p>
<br>
<form role="form" action="logout" method="POST">
   <input type="Submit" value="Logout" />
</form>
</body>
</html>
```

login.ftl:

```jsp
<html>
<head>
    <title>Login</title>
</head>
<body style="margin-left: 30px;">
<h3>Login</h3>
<br>
<form action="login" method="post">
    <#if (error?length > 0)??>
        <p style="color:darkred;">${error}</p>
    <#else>
    </#if>

    <label for="username">Username</label>
    <br>
    <input type="text" name="username">
    <br><br>
    <label for="password">Password</label>
    <br>
    <input type="password" name="password">
    <br><br>
    <input type="checkbox" name="rememberMe"> Remember Me
    <br><br>
    <input type="submit" value="Submit">
</form>
</body>
</html>
```

### Controller

  Add login/logout logic in your controller.
  
  ```java
  @Controller
  public class UserController {

	@GetMapping("/")
	public String index() {
		return "index";
	}

	@RequestMapping(value = "/login", method = RequestMethod.POST)
	public String login(HttpServletRequest req, UserCredentials cred, RedirectAttributes attr) {

			Subject subject = SecurityUtils.getSubject();

			if (!subject.isAuthenticated()) {
				UsernamePasswordToken token = new UsernamePasswordToken(cred.getUsername(), cred.getPassword(),
						cred.isRememberMe());
				try {
					subject.login(token);
				} catch (AuthenticationException ae) {
				
					attr.addFlashAttribute("error", "Invalid Credentials");
					return "redirect:/login";
				}
			}

			return "redirect:/secure";
		
	}

	@GetMapping("/secure")
	public String secure(ModelMap modelMap) {

		Subject currentUser = SecurityUtils.getSubject();

		//get roles and permissions

		modelMap.addAttribute("username", currentUser.getPrincipal());
		modelMap.addAttribute("permission", permission);
		modelMap.addAttribute("role", role);

		return "secure";
	}

	@PostMapping("/logout")
	public String logout() {
		Subject subject = SecurityUtils.getSubject();
		subject.logout();
		return "redirect:/";
	}
  ```
