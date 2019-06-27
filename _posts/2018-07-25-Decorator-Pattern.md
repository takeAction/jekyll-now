---
layout : post
title : Decorator Pattern
comments: true
categories : [Design Pattern]
---

### Decorator Pattern

Decorator pattern is used to add extension(additional features) to an existing object dynamically 
without modifying its structure. This follows the Open-Closed principle.

If one object that requires the extension but that is not suitable by design, then go for decoration.

A typical usage of Decorator pattern is Java IO classes, 
e.g. `BufferedReader input = new BufferedReader(new InputStreamReader(inputStream));`
`BufferedReader` decorates `InputStreamReader`.

Following is its diagram which from [Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e9/Decorator_UML_class_diagram.svg/600px-Decorator_UML_class_diagram.svg.png)

![_config.yml]({{ site.baseurl }}/images/decorator-pattern.png)

The `Component` can be abstract class or interface. The `Decorator` can be abstract class or normal class.

### Decorator vs Adapter

  Both them wrap an object, but adapter pattern is used to convert the interface of an object into something else. 
  
  Decorator Pattern is used to extend the functionality of an object while keeping its interface.

### Example

```java
public abstract class Pizza {
	
	String description = "Unkknown Pizza";

	public String getDescription() {
		return description;
	}

	public abstract int getCost();

}

public class PeppyPaneer extends Pizza{
	
	public PeppyPaneer() {
		description = "PeppyPaneer";
	}

	@Override
	public int getCost() {
		
		return 100;
	}

}

public class FarmHouse extends Pizza{

	@Override
	public int getCost() {
		
		return 10;
	}

	public FarmHouse() {
		
		description = "FarmHouse";
	}

}
```

```java
public abstract class ToppingsDecorator extends Pizza{
	
	protected Pizza pizza;
	
	public abstract String getDescription();

	public ToppingsDecorator(Pizza pizza) {
		
		this.pizza = pizza;
	}

}

public class FreshTomato extends ToppingsDecorator{
	
	@Override
	public String getDescription() {
		
		return this.pizza.getDescription() + ", Fresh Tomato ";
	}

	@Override
	public int getCost() {
		
		return 40 + this.pizza.getCost();
	}

	public FreshTomato(Pizza pizza) {
		super(pizza);
		
	}
}

public class Paneer extends ToppingsDecorator {
	
	@Override
	public String getDescription() {
		
		return pizza.getDescription() + ", Paneer ";
	}

	@Override
	public int getCost() {
		
		return 70 + pizza.getCost();
	}

	public Paneer(Pizza pizza) {
		super(pizza);
		
	}
}
```

```java
public class Test {

	public static void main(String[] args) {
		
		Pizza p1 = new FarmHouse();
		System.out.println( p1.getDescription() +
                " Cost :" + p1.getCost());
		
		Pizza p2 = new PeppyPaneer();
		p2 = new FreshTomato(p2);
		System.out.println( p2.getDescription() +
                " Cost :" + p2.getCost());
		
		p2 = new Paneer(p2);
		
		System.out.println( p2.getDescription() +
                " Cost :" + p2.getCost());
	}

}
```

**Note** : this example code is from [geeksforgeeks](https://www.geeksforgeeks.org/decorator-pattern-set-3-coding-the-design/)
