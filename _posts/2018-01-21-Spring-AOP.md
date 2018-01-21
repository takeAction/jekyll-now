---
layout : post
title : Spring AOP
categories : Spring
---

### Spring AOP


#### By xml

```Java
public class PaymentApply {
	
	public void apply() {
		
		String user = "user1";
		String payment = "pay for dinner";
		
		System.out.println(user+","+payment);
	}
}
```

```Java
public class Notification {
	
	public void sendNotification() {
		
		System.out.println("send message to : ");
	}
}
```

```XML
<bean id="paymentApply" class="example.my.spring.aop.PaymentApply" />
	
	<bean id="notification" class="example.my.spring.aop.Notification" />
	
	<aop:config>
		<aop:aspect id="myAspect" ref="notification" >
			<aop:pointcut id="afterPointCut" expression="execution(* example.my.spring.aop.PaymentApply.*(..))" />
			<aop:after method="sendNotification" pointcut-ref="afterPointCut" />
		</aop:aspect>
	</aop:config>
```

#### By annotation

```Java
<aop:aspectj-autoproxy/>
<bean id="paymentApply" class="example.my.spring.aop.PaymentApply" />
<bean id="notification" class="example.my.spring.aop.Notification" />
```

```
@Aspect
public class Notification {
	
	@Pointcut("execution(* example.my.spring.aop.PaymentApply.apply(..))")
	private void beforePointCunt() {}
	
	@Before("beforePointCunt()")
	public void sendNotification() {
		
		System.out.println("send message to in aop annotation ");
	}
	
}
```

#### Advice

#### Pointcut expression
