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

  Generics method looks like `public <T> void set(T t) {//code ommited}`. 
  
  The `<T>` indicates that `T` is a type parameter, if you hadn't specified it, then Java would think the `T` referred to an actual class named `T`, and ask you to import it.
  
  In this case, this type parameter scope is limited to the method where it is declared.
  
  Static and non-static generic methods are allowed, as well as generic class constructors.
  
  The syntax for a generic method includes a list of type parameters, inside angle brackets, which appears before the method's return type. 
  For static generic methods, the type parameter section must appear before the method's return type.
  
  For example,
  
  ```Java
  public class Util {
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
  }

  boolean same = Util.<Integer, String>compare(p1, p2);
  ```
  
  Generally, this can be left out and the compiler will infer the type that is needed: `boolean same = Util.compare(p1, p2);`
  
  This feature, known as **type inference**, allows you to invoke a generic method as an ordinary method, without specifying a type between angle brackets.
  
  Or you add add type parameter to class like:
  
  ```
  public class Box<T> {
  
      public void set(T t) {  }
	   
	}
  ```
  
  This type parameter's scope is class.
  
## Generics Cast

  `List<String>` cannot be casted to `List<Object>` even `String` is subtype of `Object`, but `List<String>` is not subtype of `List<Object>`.
  
  If it can, then that allows developer to add `Object` to string list, thus violating the original contract of `List<String>`.
  
  While `ArrayList<String>` can be casted to `List<String>` because `ArrayList<E>` implements `List<E>`, so `ArrayList<String>` is a subtype of `List<String>`
  
  ![_config.yml]({{ site.baseurl }}/images/java-generics.PNG)
  
## Raw type

  A raw type is the name of a generic class or interface without any type arguments.

  `public class Box<T> { }` and `Box rawBox = new Box();`

  `Box` is the raw type of the generic type `Box<T>`. However, a non-generic class or interface type is not a raw type.

  For backward compatibility, assigning a parameterized type to its raw type is allowed:

  `Box<String> stringBox = new Box<>(); Box rawBox = stringBox;// OK`
  
## Type erase

  Java compiler applies type erasure to:

  - Replace all type parameters in generic types with their bounds or Object if the type parameters are unbounded. The produced bytecode, therefore, contains only ordinary classes, interfaces, and methods.
  - Insert type casts if necessary to preserve type safety.
    e.g. `List<String> strList = ...;...String s = strList.get(0);` changed to `List<String> strList = ...;...String s = (String)strList.get(0);`
  - Generate bridge methods to preserve polymorphism in extended generic types.
  
  Type erasure ensures that no new classes are created for parameterized types; consequently, generics incur no runtime overhead.
  
  During the type erasure process, the Java compiler erases all type parameters and replaces each with its first bound if the type parameter is bounded, or Object if the type parameter is unbounded.
  
  For example,
  
  ```Java
  public class Node<T> {

    private T data;
    private Node<T> next;

    public Node(T data, Node<T> next) {
        this.data = data;
        this.next = next;
    }

    public T getData() { return data; }
    // ...
  }
  ```
  
  changed to
  
  ```Java
  public class Node {

    private Object data;
    private Node next;

    public Node(Object data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Object getData() { return data; }
    // ...
  }
  ```
  
  While for 
  
  ```Java
  public class Node<T extends Comparable<T>> {

    private T data;
    private Node<T> next;

    public Node(T data, Node<T> next) {
        this.data = data;
        this.next = next;
    }

    public T getData() { return data; }
    // ...
  }
  ```
  
  changed to
  
  ```Java
  public class Node {

    private Comparable data;
    private Node next;

    public Node(Comparable data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Comparable getData() { return data; }
    // ...
  }
  ```
  
  **Therefore, at execution time, a List\<String> and a List\<Date> are exactly the same.**
  
### Bridge method

  Take following code for example:
  
  ```Java
  public class Node<T> {

    public T data;

    public Node(T data) { this.data = data; }

    public void setData(T data) {
        System.out.println("Node.setData");
        this.data = data;
    }
 }

 public class MyNode extends Node<Integer> {
    public MyNode(Integer data) { super(data); }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }
  }
  
  MyNode mn = new MyNode(5);
  Node n = mn;            
  n.setData("Hello");  
  ```
  
  The `n.setData("Hello");` will throw ClassCastException:String cannot be cast to Integer.
  
  After type erasure, the Node and MyNode classes become:
  ```
  public class Node {

    public Object data;

    public Node(Object data) { this.data = data; }

    public void setData(Object data) {
        System.out.println("Node.setData");
        this.data = data;
    }
  }

  public class MyNode extends Node {

    public MyNode(Integer data) { super(data); }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }
  }
  ```
  
  After type erasure, the method signatures do not match. The Node method becomes setData(Object) and the MyNode method becomes setData(Integer). Therefore, the MyNode setData method does not override the Node setData method.
  
  And `Node n = mn;` changed to `Node n = (MyNode)mn;` after type erasure. 
  
  As a consequence, we cannot pass string to `n.setData("Hello");`, as it should be an `Integer` since `mn` is a `MyNode<Integer>` type.
  
  To solve this problem and preserve the polymorphism of generic types after type erasure, a Java compiler generates a bridge method to ensure that subtyping works as expected. For the MyNode class, the compiler generates the following bridge method for setData:
  ```Java
  class MyNode extends Node {

    // Bridge method generated by the compiler
    public void setData(Object data) {
        setData((Integer) data);
    }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }

    // ...
  }
  ```
  
## Restrictions

  - Cannot Instantiate Generic Types with Primitive Types
  - Cannot Create Instances of Type Parameters
    As a workaround, you can create an object of a type parameter through reflection:
    ```Java
    public static <E> void append(List<E> list, Class<E> cls) throws Exception {
    E elem = cls.newInstance();  
    list.add(elem);
    }
    ```
  - Cannot Declare Static Fields Whose Types are Type Parameters
  - Cannot Use Casts or instanceof With Parameterized Types
    Because the Java compiler erases all type parameters in generic code, you cannot verify which parameterized type for a generic type is being used at runtime:
    ```Java
    public static <E> void rtti(List<E> list) {
        if (list instanceof ArrayList<Integer>) {  // compile-time error
        // ...
        }
    }
    ```
    The runtime does not keep track of type parameters, so it cannot tell the difference between an `ArrayList<Integer>` and an `ArrayList<String>`. The most you can do is to use an unbounded wildcard to verify that the list is an ArrayList:
    ```
    public static void rtti(List<?> list) {
        if (list instanceof ArrayList<?>) {  // OK; instanceof requires a reifiable type
        // ...
        }
    }
    ```
  - Cannot Create Arrays of Parameterized Types
  - Cannot Create, Catch, or Throw Objects of Parameterized Types
    A generic class cannot extend the Throwable class directly or indirectly.
  - Cannot Overload a Method Where the Formal Parameter Types of Each Overload Erase to the Same Raw Type
  - Cannot Instantiate Generic Types with Primitive Types
  
## How to see the code after erasure

  You can see what the code looks like after generic type erasure by `javac -XD-printflat -d output_dir SomeFile.java`
  
## References

  - [Oracle Generics](https://docs.oracle.com/javase/tutorial/java/generics/)
  - [Oracle Tutorial](https://docs.oracle.com/javase/tutorial/extra/generics/intro.html)
