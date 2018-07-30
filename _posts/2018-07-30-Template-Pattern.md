---
layout : post
title : Template Method Pattern
comments: true
categories : Java
---

### Template Method Pattern

  It defines a workflow to finish specific operations/steps which have order.
  
  It allows subclasses to change step(s) detail without modifying the workflow's structure.
  
  Optionally it can provide default implementation base on your requirements.

  ![_config.yml]({{ site.baseurl }}/images/template-pattern.png)
  
  For above diagram, 
  1. `XXTemplate` is an abstract class
  2. `templateMethod` is a final method which consist of operation steps, `final` makes it cannot be overrid by subclasses
  3. `step1()` and `step2()` are abstract methods
  4. `step3()` is default implementations(optionally)
  5. template method of superclass call method of subclasses, this is known as Hollywood Principle – “don’t call us, we’ll call you.”.
  
### Example

  ```java
  public abstract class OrderProcessTemplate {
	
	public boolean isGift;
	
	public abstract void doSelect();
	
  public  abstract void doPayment();
	
	public abstract void doDelivery();
	
	public final void wrapGift() {
		
		System.out.println("Gift wrap successfull");
	}
	
	public final void processOrder(boolean isGift) {
		
		doSelect();
		
		doPayment();
		
		if( isGift ) {
			wrapGift();
		}
		
		doDelivery();
		
	}

  }
  
  public class OnlineOrder extends OrderProcessTemplate {

	@Override
	public void doSelect() {
		
		System.out.println("OnlineOrder.doSelect");
	}

	@Override
	public void doPayment() {
		
		System.out.println("OnlineOrder.doPayment");
	}

	@Override
	public void doDelivery() {
		
		System.out.println("OnlineOrder.doDelivery");
	}

  }
  
  public class OnSiteOrder extends OrderProcessTemplate {

	@Override
	public void doSelect() {
		
		System.out.println("OnSiteOrder.doSelect");
	}

	@Override
	public void doPayment() {
		
		System.out.println("OnSiteOrder.doPayment");
	}

	@Override
	public void doDelivery() {
		
		System.out.println("OnSiteOrder.doDelivery");
	}

  }
  
  public class Test {

	public static void main(String[] args) {
		
		OrderProcessTemplate order = new OnlineOrder();
		order.processOrder(false);
		
		order = new OnSiteOrder();
		order.processOrder(true);
	}

  }

  ```
  
  reference [template-method-design-pattern](https://www.geeksforgeeks.org/template-method-design-pattern/)
