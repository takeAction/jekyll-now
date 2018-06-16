---
layout : post
title : Java Generics
comments: true
categories : Java
---

  Generics were introduced to the Java language to provide tighter type checks at compile time and to support generic programming. 
  
  The generics looks like:
  ```Java
  List<String> list = new ArrayList<>();
  //add item to list
  String s = list.get(0);
  ```
  or
  ```Java
  public class Box<T> {
	    // T stands for "Type"
	    private T t;
      //other code
	}
  ```
  
  The most commonly used type parameter names are:
  ```
  E - Element (used extensively by the Java Collections Framework)
  K - Key
  N - Number
  T - Type
  V - Value
  S,U,V etc. - 2nd, 3rd, 4th types
  ```
  
## Wildcards

### Unbounded Wildcards

  `List<?>` - pronounced "list of unknown", that is, a list whose element type matches anything, `?` stands for any unknown type.
  
  ```Java
  Collection<?> c = new ArrayList<String>();
  c.add(new Object()); // Compile time error
  ```
  
  Since we don't know what the element type of c stands for, we cannot add objects to it.   
 
  When the actual type parameter is ?, it stands for some unknown type. 
  Any parameter we pass to add would have to be a subtype of this unknown type. 
  Since we don't know what type that is, we cannot pass anything in. The sole exception is null, which is a member of every type.
  
### Upper Bounded Wildcards

  `<? extends type>` stands for type and its sub-type. e.g. `List<? extends Number>` accepts List of Number and any subtype of Number.
  
  **In this context, extends is used in a general sense to mean either "extends" (as in classes) or "implements" (as in interfaces).**
  
  In addition, multiple bounds such as `<T extends B1 & B2 & B3>` is allowed.
  
  For multiple bound, if one of the bounds is a class, it must be specified first. For example:
  ```Java
  Class A { /* ... */ }
  interface B { /* ... */ }
  interface C { /* ... */ }  
  ```
  
  `class D <T extends A & B & C> { /* ... */ }` is correct, while `class D <T extends B & A & C> { /* ... */ }` has compile-time error.

### Lower Bounded Wildcards

  The wildcard `<? super type>` matches type, as well as its super-type.
  
## Type parameter and Type argument

  The `T` in `Foo<T>` is a type parameter and the `String` in `Foo<String> f` is a type argument.
  
## Generics method

  Generics method looks like `public <T> void set(T t) {//code ommited}`. The `<T>` indicates that `T` is a type parameter, 
  if you hadn't specified it, then Java would think the `T` referred to an actual class named `T`, and ask you to import it. 
  
  Or you add add type parameter to class like:
  
  ```
  public class Box<T> {
  
      public void set(T t) {  }
	   
	}
  ```
  
## Type erase

  Java compiler applies type erasure to:

  - Replace all type parameters in generic types with their bounds or Object if the type parameters are unbounded. The produced bytecode, therefore, contains only ordinary classes, interfaces, and methods.
  - Insert type casts if necessary to preserve type safety.
  - Generate bridge methods to preserve polymorphism in extended generic types.
  
  Type erasure ensures that no new classes are created for parameterized types; consequently, generics incur no runtime overhead.

  
## References

  - [Oracle Generics](https://docs.oracle.com/javase/tutorial/java/generics/)
  - [Oracle Tutorial](https://docs.oracle.com/javase/tutorial/extra/generics/intro.html)
