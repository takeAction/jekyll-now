---
layout : post
title : Builder Pattern
comments: true
categories : [Design Pattern]
---

### Builder Pattern

  It aims to “Separate the construction of a complex object from its representation so that 
  the same construction process can create different representations.” 
  
  It is used to construct a complex object step by step and the final step will return the object.
  
  The builder has 4 main parts: a `Builder`, `Concrete Builders`, a `Director`, and a `Product`.
  
  From [JournalDev](https://www.journaldev.com/1425/builder-design-pattern-in-java):
  
  > Builder pattern was introduced to solve some of the problems with Factory and Abstract Factory design patterns 
  > when the Object contains a lot of attributes.
  > 
  > There are three major issues with Factory and Abstract Factory design patterns 
  > when the Object contains a lot of attributes.
  > 
  > Too Many arguments to pass from client program to the Factory class that can be error prone because most of the time, 
  > the type of arguments are same and from client side its hard to maintain the order of the argument.
  > Some of the parameters might be optional but in Factory pattern, we are forced to send all the parameters 
  > and optional parameters need to send as NULL.
  > If the object is heavy and its creation is complex, then all that complexity will be part of Factory classes 
  > that is confusing.
  >
  > We can solve the issues with large number of parameters by providing a constructor with required parameters 
  > and then different setter methods to set the optional parameters. The problem with this approach is that 
  > the Object state will be inconsistent until unless all the attributes are set explicitly.
  >
  > Builder pattern solves the issue with large number of optional parameters and inconsistent state by 
  > providing a way to build the object step-by-step and provide a method that will actually return the final Object.
  
### Difference with Factory

  From [HowToDoInJava](https://howtodoinjava.com/design-patterns/creational/builder-pattern-in-java/)
  
  > The only big difference between this builder pattern and abstract factory pattern is that, 
  > builder provides you more control over the object creation process and that’s it. 
  > Apart from it, there are no major differences.
  > 
  > In one sentence, abstract factory pattern is the answer to "WHAT" and the builder pattern to "HOW".
  
  Builder pattern is used when there are many steps to create an object.
  
  Factory pattern is used when the factory can easily create the entire object within one method call.
  
### Code

  From [programcreek](https://www.programcreek.com/2013/02/java-design-pattern-builder/)

```java
public class Starbucks {

	private String size;
	private String drink;
 
	public void setSize(String size) {
		this.size = size;
	}
 
	public void setDrink(String drink) {
		this.drink = drink;
	}
}

public abstract class StarbucksBuilder {

	protected Starbucks starbucks;
 
	public Starbucks getStarbucks() {
		return starbucks;
	}
 
	public void createStarbucks() {
		starbucks = new Starbucks();
		System.out.println("a drink is created");
	}
 
	public abstract void buildSize();
	public abstract void buildDrink();
}

public class TeaBuilder extends StarbucksBuilder {

	public void buildSize() {
		starbucks.setSize("large");
		System.out.println("build large size");
	}
 
	public void buildDrink() {
		starbucks.setDrink("tea");
		System.out.println("build tea");
	}
 
}

public class CoffeeBuilder extends StarbucksBuilder {

	public void buildSize() {
		starbucks.setSize("medium");
		System.out.println("build medium size");
	}
 
	public void buildDrink() {
		starbucks.setDrink("coffee");
		System.out.println("build coffee");
	}
}

public class Waiter {

	private StarbucksBuilder starbucksBuilder;
 
	public void setStarbucksBuilder(StarbucksBuilder builder) {
		starbucksBuilder = builder;
	}
 
	public Starbucks getstarbucksDrink() {
		return starbucksBuilder.getStarbucks();
	}
 
	public void constructStarbucks() {
		starbucksBuilder.createStarbucks();
		starbucksBuilder.buildDrink();
		starbucksBuilder.buildSize();
	}
}

public class Test {

	public static void main(String[] args) {
  
    Waiter waiter = new Waiter();
		StarbucksBuilder coffeeBuilder = new CoffeeBuilder();
 
		waiter.setStarbucksBuilder(coffeeBuilder);
		waiter.constructStarbucks();
 
		Starbucks drink = waiter.getstarbucksDrink();
  }

}
```
