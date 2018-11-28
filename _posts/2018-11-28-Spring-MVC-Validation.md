---
layout : post
title : Spring MVC Validation
comments: true
categories : Spring
---

  In spring mvc, we can utilize [JSR 303](https://beanvalidation.org/1.0/) and [JSR 380](https://beanvalidation.org/2.0/) specification
  to make validation easier.
  
  Note: If you already depend on `spring-boot-starter-web`, then you already have Spring Validation. 
  
  Otherwise, you’ll have to include `spring-context`.
  
### Code

#### Bean

```java
import java.io.Serializable;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

public class User implements Serializable{	
	
	private static final long serialVersionUID = -7561411960266528475L;

	@NotNull(message = "id cannot be null")
	@Min(1)
	private Integer userId;
	
	@NotNull
	@Size(min = 1,max = 3, message = "use name length must be between 1 and 3")
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
```

#### Result Message

```java
public class ResultMessage {
	
	private boolean isSuccess;
  private String code;	
	private String msg;
	
	public boolean isSuccess() {
		return isSuccess;
	}

	public void setSuccess(boolean isSuccess) {
		this.isSuccess = isSuccess;
	}

	public String getCode() {
		return code;
	}

	public void setCode(String code) {
		this.code = code;
	}

	public String getMsg() {
		return msg;
	}

	public void setMsg(String msg) {
		this.msg = msg;
	}
}
```

#### Controller

```java
@RequestMapping("getUser")
	public User getUser(@RequestBody @Valid User user) {
		
		return this.userService.getUser(user.getUserId());
	}
```

##### @Valid and @Validated

  `@Validated` was added to support "validation groups". For example,
  
  ```java
  @NotBlank(groups = {StepTwo.class})
  private String name;
  
  @Email(groups = {StepOne.class})
  private Stirng email;
  ```
  
  ```java
  @RequestMapping(value = "stepOne")
  public String stepOne(@Validated(User.StepOne.class) User user) {//...}
  ```

#### Exception Handler

```java
import java.util.List;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.ResponseStatus;
import com.example.sb.model.ResultMessage;
import org.springframework.http.HttpStatus;
import org.springframework.validation.ObjectError;

@ControllerAdvice
public class CustomExceptionHandler {
	
	@ResponseStatus(HttpStatus.BAD_REQUEST)
	@ExceptionHandler(MethodArgumentNotValidException.class)
	public @ResponseBody ResultMessage handleValidationExceptions(MethodArgumentNotValidException ex) {
		
		List<ObjectError> errors = ex.getBindingResult().getAllErrors();
		ResultMessage result = new ResultMessage();
		StringBuilder sb = new StringBuilder();
		for(ObjectError err : errors ) {			
			sb.append(err.getDefaultMessage());
		}
	
		result.setSuccess(false);
		result.setCode("Validate failed");
		result.setMsg(sb.toString());
		
	    return result;
	}
  
  @ExceptionHandler(ConstraintViolationException.class)  
  @ResponseStatus(HttpStatus.BAD_REQUEST)
  public @ResponseBody ResultMessage handle(ConstraintViolationException exception) {
         //.....
    }

}
```

**Note: @Valid annotation results in MethodArgumentNotValidException being thrown when validation fails, 
   but @Validated results in ConstraintViolationException being thrown.**
   
##### @ExceptionHandler

  It is used to handle exceptions and errors by annotating it on a method.
  
  Developer can create a method within a controller with this annotation to handle exception.
  
  If you want to apply this logic to all controllers, you should create a new class annotated with the `@ControllerAdvice`
  
### Custom Validations
  
### References

[g00glen00b](https://g00glen00b.be/validating-the-input-of-your-rest-api-with-spring/)

[Spring](https://spring.io/guides/gs/validating-form-input/)
