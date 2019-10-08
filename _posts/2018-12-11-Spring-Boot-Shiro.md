---
layout : post
title : Spring Boot Shiro
comments: true
categories : Spring
---

Integrate Apache Shiro into spring boot 2.

### Add dependency

  Add `shiro-spring-boot-web-starter` to maven pom.

### Shiro in yaml

```yml
shiro:
  loginUrl: /login
  successUrl: /secure
  unauthorizedUrl: /login
  sessionManager:
    sessionIdUrlRewritingEnabled: false
```

### Implements Custom Realm

```java
public class UserRealm extends AuthorizingRealm {
	
	@Autowired
	private CustomerService userService;

	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {

		Subject currentUser = SecurityUtils.getSubject();

		User user = (User) currentUser.getSession().getAttribute("user");

		if (user != null) {

			SimpleAuthorizationInfo authorizationInfo = new SimpleAuthorizationInfo();

			authorizationInfo.addRoles(user.getRoleList());

			return authorizationInfo;

		}

		return null;
	}

	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {

		UsernamePasswordToken upToken = (UsernamePasswordToken) token;

		User user = new User();

		//set user info from token

		User dbUser = this.userService.getUser(user);	

		if (dbUser != null) {
			
			Subject currentUser = SecurityUtils.getSubject();

			currentUser.getSession().setAttribute("user", dbUser);

			return new SimpleAuthenticationInfo(upToken.getUsername(), dbUser.getPwd().toCharArray(), getName());
		}

		return null;
	}

}
```

### Shiro Config

```java
@Configuration
public class ShiroConfig {

	@ExceptionHandler(AuthorizationException.class)
	@ResponseStatus(HttpStatus.FORBIDDEN)
	public String handleException(AuthorizationException e, Model model) {		

		Map<String, Object> map = new HashMap<String, Object>();

		map.put("status", HttpStatus.FORBIDDEN.value());

		map.put("message", "No message available");

		model.addAttribute("errors", map);

		return "error";

	}

	@Bean
	public UserRealm myShiroRealm() {

		UserRealm myShiroRealm = new UserRealm();

		myShiroRealm.setCredentialsMatcher(hashedCredentialsMatcher());

		return myShiroRealm;

	}

	@Bean
	public HashedCredentialsMatcher hashedCredentialsMatcher() {

		HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();

		hashedCredentialsMatcher.setHashAlgorithmName("MD5");	

		return hashedCredentialsMatcher;

	}

	@Bean
	public ShiroFilterChainDefinition shiroFilterChainDefinition() {

		DefaultShiroFilterChainDefinition chainDefinition = new DefaultShiroFilterChainDefinition();	

		chainDefinition.addPathDefinition("/", "anon");
		chainDefinition.addPathDefinition("/login", "anon");
		
		chainDefinition.addPathDefinition("/logout", "logout");
			
		chainDefinition.addPathDefinition("/**/*", "authc");

		return chainDefinition;
	}

}
```

### Login

  Add login logic in your controller.
  
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
					subject.login(token);//
				} catch (AuthenticationException ae) {
				
					attr.addFlashAttribute("error", "Invalid Credentials");
					return "redirect:/login";
				}
			}

			return "redirect:/secure";
		
	}
}	
```

### Shiro ajax

When session timeout, if a secure resource is called by ajax, there is no any hints to user or page cannot be redirected to login page
by default.

In order to handle this case, developer needs to override `onAccessDenied` of `FormAuthenticationFilter` to return specified response status when session timeout:

```java
public class CustomShiroAuthcFilter extends FormAuthenticationFilter{
	
	public CustomShiroAuthcFilter() {
		
		super();
	}
	
	@Override
    protected boolean onAccessDenied(ServletRequest request, ServletResponse response) throws Exception {
		
        if (isLoginRequest(request, response)) {
            return super.onAccessDenied(request, response);
        } else {
        	
            if (isAjax((HttpServletRequest) request)) {
                HttpServletResponse httpServletResponse = WebUtils.toHttp(response);
                
                httpServletResponse.setStatus(HttpStatus.UNAUTHORIZED.value());
            } else {
                saveRequestAndRedirectToLogin(request, response);
            }
            
            return false;
        }
    }

    private boolean isAjax(HttpServletRequest request) {
    	
        String requestedWithHeader = request.getHeader("X-Requested-With");
        return "XMLHttpRequest".equals(requestedWithHeader);
    }

}
```

and add `ShiroFilterFactoryBean` manually:

```java
@Configuration
public class ShiroConfig {

	@ExceptionHandler(AuthorizationException.class)
	@ResponseStatus(HttpStatus.FORBIDDEN)
	public String handleException(AuthorizationException e, Model model) {		

		Map<String, Object> map = new HashMap<String, Object>();

		map.put("status", HttpStatus.FORBIDDEN.value());

		map.put("message", "No message available");

		model.addAttribute("errors", map);

		return "error";

	}

	@Bean("authorizer")
	public UserRealm myShiroRealm() {

		UserRealm myShiroRealm = new UserRealm();

		myShiroRealm.setCredentialsMatcher(hashedCredentialsMatcher());

		return myShiroRealm;

	}

	@Bean
	public HashedCredentialsMatcher hashedCredentialsMatcher() {

		HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();

		hashedCredentialsMatcher.setHashAlgorithmName("MD5");	

		return hashedCredentialsMatcher;

	}

	 @Bean
	  public ShiroFilterFactoryBean shiroFilterFactoryBean(SessionsSecurityManager securityManager) {
		
	    ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
	    
	   
	    shiroFilterFactoryBean.setSecurityManager(securityManager);
	    
	    shiroFilterFactoryBean.setLoginUrl("/");
	    shiroFilterFactoryBean.setSuccessUrl("/main");
	    shiroFilterFactoryBean.setUnauthorizedUrl("/");	    
	   
	    Map<String, Filter> filtersMap = new LinkedHashMap<>();
	   
	    filtersMap.put("authc", new CustomShiroAuthcFilter());
	    shiroFilterFactoryBean.setFilters(filtersMap);
	   
	    Map<String, String> filterChainDefinitionMap = new LinkedHashMap<>();
	    filterChainDefinitionMap.put("/", "anon");
	    filterChainDefinitionMap.put("/login", "anon");
	    filterChainDefinitionMap.put("/css/*", "anon");
	    filterChainDefinitionMap.put("/image/*", "anon");
	    filterChainDefinitionMap.put("/logout", "logout");
	    filterChainDefinitionMap.put("/index", "anon");
	    filterChainDefinitionMap.put("/**/*", "authc");
	    shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);
	    
	    return shiroFilterFactoryBean;
	  }

}
```
