---
layout : post
title : Spring RestTemplate
comments: true
categories : Spring
---

There are many approaches to do restful call, e.g. Spring `RestTemplate`.

In this post, we use spring boot to illustrate.

1. create `RestTemplate` bean in spring boot application

```
@SpringBootApplication
public class MvcApplication {

	public static void main(String[] args) {
		SpringApplication.run(MvcApplication.class, args);
	}

	@Bean
	public RestTemplate getRestTemplate() {
		
		return new RestTemplate();
	}

}
```

2. autowire `RestTemplate`

```
@Controller
@RequestMapping("/hello/")
public class Hello {
	
	@Autowired
	RestTemplate restTemplate;
	
	// ....

}
```

3. call REST services

  `RestTemplate` provides methods to call rest services, e.g. `getForObject`, `postForObject`, `exchange`.
  
  For details, please refer to its api.

```
@GetMapping("callApi")
	public ModelAndView callApi(Model model) {
		
		String result = restTemplate.getForObject("http://localhost:8080/jwt/hello/getMsg", String.class);
		
		model.addAttribute("msg", result);
		return new ModelAndView("hello");
	}
```

the server side looks like:

```
@RestController
@RequestMapping("/hello/")
public class Hello {
	
	@RequestMapping("getMsg")
	public String getMsg() {
		
		return "msg from jwt project";
	}

}
```
