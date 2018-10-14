---
layout : post
title : Chain of Responsibility Pattern
comments: true
categories : Design Pattern
---

### Chain of Responsibility

  A request from client is passed to a chain of handler to process them. 
	
  Later, the handler in the chain will decide who will process the request 
  and whether send the request to next handler in the chain.

  Please note that a request is not handled by any handler is a valid use case.

#### Advantages

  1. Chain of responsibility pattern is used to achieve loose coupling between the sender and handler
  2. Extra handlers can be added or removed from chain without modifying the logic inside any of concrete handler.

#### Disadvantages

  It comes with the trade-off of having a lot of implementation classes and maintenance problems if most of the code is common in all the implementations.

#### Example in JDK

  1. java.util.logging.Logger.log()
  2. javax.servlet.Filter.doFilter()

#### Code

  From [geeksforgeeks](https://www.geeksforgeeks.org/chain-responsibility-design-pattern/)

```java
public interface Chain {
	
    void setNext(Chain nextInChain); 
    void process(int request); 

}

public class NegativeProcessor implements Chain {
	
	private Chain nextInChain;

	public void setNext(Chain c) {
		nextInChain = c;
	}

	public void process(int request) {
	
	    if (request < 0) {
		System.out.println("NegativeProcessor : " + request.getNumber());
	    } else {
		nextInChain.process(request);
	    }
	}
}

public class ZeroProcessor implements Chain {

	private Chain nextInChain;

	public void setNext(Chain c) {
		nextInChain = c;
	}

	public void process(int request) {
	
	    if (request == 0) {
		System.out.println("ZeroProcessor : " + request.getNumber());
	    } else {
		nextInChain.process(request);
	    }
	}
}

public class PositiveProcessor implements Chain {

	private Chain nextInChain;

	public void setNext(Chain c) {
		nextInChain = c;
	}

	public void process(int request) {
	
	    if (request > 0) {
		System.out.println("PositiveProcessor : " + request.getNumber());
	    } else {
		nextInChain.process(request);
	    }
	}
}

public class Test {

    public static void main(String[] args) {
		
        Chain c1 = new NegativeProcessor(); 
        Chain c2 = new ZeroProcessor(); 
        Chain c3 = new PositiveProcessor(); 
        c1.setNext(c2); 
        c2.setNext(c3);   
        
        c1.process(90); 
        c1.process(-50); 
        c1.process(0); 
      
    }

}
```
