---
layout : post
title : Singleton Pattern
comments: false
categories : Design Pattern
---

### Singleton Pattern

This post is from [geeksforgeeks](https://www.geeksforgeeks.org/singleton-design-pattern/):

#### Classic Implementation

```java
class Singleton {

    private static Singleton obj;

    private Singleton() {}

    public static Singleton getInstance()
    {
        if (obj==null)
            obj = new Singleton();

        return obj;
    }
}
```

The main problem with above method is that it is not thread safe.

#### make getInstance() synchronized

```java
class Singleton {

    private static Singleton obj;

    private Singleton() {}

    // Only one thread can execute this at a time
    public static synchronized Singleton getInstance() {

        if (obj==null)
            obj = new Singleton();

        return obj;
    }
}
```

The main disadvantage of this is method is that using synchronized
every time while creating the singleton object is expensive and
may decrease the performance of your program.

However if performance of getInstance() is not critical for your application
this method provides a clean and simple solution.

#### Eager Instantiation

```java
class Singleton {

    private static Singleton obj = new Singleton();

    private Singleton() {}

    public static Singleton getInstance() {
        return obj;
    }
}
```

JVM executes static initializer when the class is loaded and
hence this is guaranteed to be thread safe.
Use this method only when your singleton class is light and
is used throughout the execution of your program.

#### Double Checked Locking

```java
class Singleton {

    private volatile static Singleton obj;

    private Singleton() {}

    public static Singleton getInstance() {

        if (obj == null) {
            // To make thread safe
            synchronized (Singleton.class)
            {
                // check again as multiple threads
                // can reach above step
                if (obj==null)
                    obj = new Singleton();
            }
        }

        return obj;
    }
}
```

We have declared the obj volatile which ensures that multiple threads
offer the obj variable correctly when it is being initialized to
Singleton instance. This method drastically reduces the overhead of
calling the synchronized method every time.

### Cases that break Singleton

#### Reflection

```java
public class Singleton {

    public static Singleton instance = new Singleton();

    private Singleton()  {

    }
}

public class GFG {

    public static void main(String[] args) {

        Singleton instance1 = Singleton.instance;
        Singleton instance2 = null;
        try {
            Constructor[] constructors =
                    Singleton.class.getDeclaredConstructors();
            for (Constructor constructor : constructors) {
                // Below code will destroy the singleton pattern
                constructor.setAccessible(true);
                instance2 = (Singleton) constructor.newInstance();
                break;
            }
        } catch (Exception e) {
            e.printStackTrace();
        }

    System.out.println("instance1.hashCode():- "
                                      + instance1.hashCode());
    System.out.println("instance2.hashCode():- "
                                      + instance2.hashCode());
    }
}
```

After running this class, you will see that hashCodes are different
that means, 2 objects of same class are created and singleton pattern
has been destroyed.

##### Solution

Use `Enum` to overcome reflection issue.

#### Serialization

Serialization is used to convert an object of byte stream and
save in a file or send over a network.

Suppose you serialize an object of a singleton class.
Then if you de-serialize that object it will create a new instance
and hence break the singleton pattern.

```java
class Singleton implements Serializable
{
    // public instance initialized when loading the class
    public static Singleton instance = new Singleton();

    private Singleton()
    {
        // private constructor
    }
}


public class GFG
{

    public static void main(String[] args)
    {
        try
        {
            Singleton instance1 = Singleton.instance;
            ObjectOutput out
                = new ObjectOutputStream(new FileOutputStream("file.text"));
            out.writeObject(instance1);
            out.close();

            // deserailize from file to object
            ObjectInput in
                = new ObjectInputStream(new FileInputStream("file.text"));

            Singleton instance2 = (Singleton) in.readObject();
            in.close();

            System.out.println("instance1 hashCode:- "
                                                 + instance1.hashCode());
            System.out.println("instance2 hashCode:- "
                                                 + instance2.hashCode());
        } catch (Exception e)  {
            e.printStackTrace();
        }
    }
}
```

##### Solution

To overcome this issue, we have to implement method `readResolve()` method.

```java
class Singleton implements Serializable
{
    public static Singleton instance = new Singleton();

    private Singleton()
    {

    }

    protected Object readResolve()
    {
        return instance;
    }
}
```

#### Cloning

Suppose, we ceate clone of a singleton object, then it wil create a copy
that is there are two instances of a singleton class,
hence the class is no more singleton.

##### Solution

Override the `clone` method.
