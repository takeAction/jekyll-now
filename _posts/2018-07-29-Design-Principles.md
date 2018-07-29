---
layout : post
title : Design Principles
comments: true
categories : Java
---

There are 5 most recommended design principles when building your application. They are called **S.O.L.I.D**

- **S**ingle Responsibility
- **O**pen Closed
- **L**iskov's Substitution
- **I**nterface Segregation
- **D**ependency Inversion

### Single Responsibility

  One class/method should have only one responsibility.
  
  In other words, there is only one reason to change your class/method. If not,  you should refactor the class/method into 
  multiple classes/methods according to functionality.
  
#### Example

```java
public class BillingService {
    
    public void confirmBilling() {
      
        //1. logic of checking whether this billing can be confirmed
        
        //2. confirm billing logic
    }
}
```

Above `confirmBilling` violates single responsibility because if checking logic changed we need to modify this method 
or if confirm logic changed we need to modify this method too.

### Open Closed

  Class/function is open for extension and close for modifying.
  
#### Example

```java
    public class Rectangle {
        //fields and their setter/getter
    }
    
    public class AreaCalculator {
        
        public double calculateRectangleArea(Rectangle r) {
            //logic of caculating rectangle area
        }
    }
```

This breaks open-closed principle.

If we need to calculate circle, we have to add `Circle` class and `calculateCircleArea(Circle c)` in `AreaCalculator`.

As a result, the `AreaCalculator` will be changed each time when there is new shape is added. 
So this design is not close for modification.

Meanwhile those changes in the existing code should be minimized, since the existing code is already unit tested 
and changes might affect the existing functionality in an unwanted manner.

The good design which follow open-closed is:

```java
    public interface Shape {
        public void calculateArea();
    }
    
    public class Circle implements Shape {
        //fields of cricle
        
        public void calculateArea() {
            //calculate area of circle
        }
    }
```

  Whenever there is new shape to be added, just extend the `Shape`.

### Liskov's Substitution

  Anywhere the base class is used can be replaced by derived classes without any undesired results.
  
#### Example
  
  > In mathematics, a Square is a Rectangle. Indeed it is a specialization of a rectangle. 
  > The "is a" makes you want to model this with inheritance. However if in code you made Square derive from Rectangle, 
  > then a Square should be usable anywhere you expect a Rectangle. This makes for some strange behavior.
  > 
  > Imagine you had SetWidth and SetHeight methods on your Rectangle base class; this seems perfectly logical. 
  > However if your Rectangle reference pointed to a Square, then SetWidth and SetHeight doesn't make sense 
  > because setting one would change the other to match it. 
  > In this case Square fails the Liskov Substitution Test with Rectangle 
  > and the abstraction of having Square inherit from Rectangle is a bad one.

### Interface Segregation

  Clients should not be forced to implement unnecessary methods which they will not use.
  
#### Example

  There is interface `ReportService` and two methods `generateExcel()` and `generatedPdf()`. 
  
  Now client ‘A’ wants to use this interface but he only wants the pdf method. 

  However, in this design,  these two methods have to be implemented even the `generateExcel()` can be left blank.
  
  Thus in the principle of interface segregation, there should be twon interfaces `PdfReport` and `ExcelReport`.
 
### Dependency Inversion

  Depend on abstractions, not on concretions.
