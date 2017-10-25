### Java Interface
- an interface is implicitly abstract, so you do not need to use abstract keyword while declaring interface
- all fields are automatically public, static and final
- as of java 8, interface can define default method and static method, other methods are implicitly abstract, so the abstract modifier is unnecessary
- methods in interface are implicitly public, cannot be other modifier
- it can be declared public or package scope(no access modifier)
- interface only can **extends** interface(one or more)
- subclass needs to provide unimplementation methods

> Checked exceptions should not be declared on implementations methods other than the ones declared by the interface method
or subclasses of those declared by the interface method.

#### Tagging interface
An interface with no methods in it is refered to as a tagging interface, e.g.
```
public interface EventListener {}
```
There are two basic design purposes:
1. creates a common parent - as with the EventListener interface, which is extended by dozens of other interfaces in the java api
   you can use a tagging interface to create a common parent among a group of interfaces, for example, when an interface
   extends EventListener, the JVM knows that this particular interface is going to be used in an event delegation scenario
2. adds a data type to a class - this situtaion is where the term, tagging comes from. A class that implements a tagging interface
   does not need to define any methods, but the class becomes an interface type through polymorphism.

### Java Abstract Class
Abstract class similar normal class except that:
- it has **abstract** keyword in class declaration
- it may or may not include abstract methods
- it cannot be instantiated
- if a class includes abstract method, then this class must be declared abstract
- it can implement interface without providing the implementation of interface methods
- subclass needs to provide implementation for all of the abstract methods, if not, 
  then this subclass must also be declared abstract
  
### Comparison
According to the Oracle doc, 

consider using abstract classes if any of these statements apply to your situation:
- You want to share code among several closely related classes.
- You expect that classes that extend your abstract class have many common methods or fields, or require access modifiers other than public (such as protected and private).
- You want to declare non-static or non-final fields. This enables you to define methods that can access and modify the state of the object to which they belong.
			
Consider using interfaces if any of these statements apply to your situation:
- You expect that unrelated classes would implement your interface. For example, the interfaces Comparable and Cloneable are implemented by many unrelated classes.
- You want to specify the behavior of a particular data type, but not concerned about who implements its behavior.
- You want to take advantage of multiple inheritance of type.
