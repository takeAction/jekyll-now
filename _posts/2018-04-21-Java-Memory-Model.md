---
layout : post
title : Java Memory Model
categories : Java
---

### Java Memory Model

  JVM is a model of whole computer so this model naturally includes a memory model - Java Memory Model(a.k.a JMM).
  
### Computer Memory

  ![_config.yml]({{ site.baseurl }}/images/hardward-memory.png)
  
  Computer with 2 or more cpus is able to run one thread per cpu concurrently.
  
  CPU can access **register** much faster than **cpu cache memory**, and cpu cache memory faster than **RAM**.
  
  All cpus can access the main memory.
  
  > Typically, when a CPU needs to access main memory it will read part of main memory into its CPU cache. 
  > It may even read part of the cache into its internal registers and then perform operations on it. 
  > When the CPU needs to write the result back to main memory it will flush the value from its internal register to the cache memory, 
  > and at some point flush the value back to main memory.
  > 
  > The values stored in the cache memory is typically flushed back to main memory when the CPU needs to store something 
  > else in the cache memory. 
  > The CPU cache can have data written to part of its memory at a time, and flush part of its memory at a time. 
  > It does not have to read / write the full cache each time it is updated. 
  > Typically the cache is updated in smaller memory blocks called "cache lines". 
  > One or more cache lines may be read into the cache memory, and one or mor cache lines may be flushed back to main memory again.
  
### JMM
  
  ![_config.yml]({{ site.baseurl }}/images/jmm.png)
  
  JMM consist of heap and thread stack.
  
#### JMM Thread Stack

  ![_config.yml]({{ site.baseurl }}/images/jmm2.png)
  
  - Each thread has its own thread stack in JMM
  - The thread stack contains the methods which the thread call and its local variables.
  - A thread can only access its own thread stack
  - Local variables created by a thread are invisible for other threads, even if two threads are executing the exact same code,
    the two treads will still create the local variables of that code in their own thread stack.
    
##### Local variables

  A local variable may be
  1. primitive type, e.g. int, these variables are stored on the thread stack.
  2. a reference to an object. In that case the reference(the local variable) is kept on the thread stack,
  but the object itself is stored on the heap
  
  One thread may pass a copy of a primitive variable to another thread, but it cannot share the primitive local variable itself.
     
#### JMM Heap

  The heap stores following information:
  1. The objects, regardless of what thread create the object.
  
     This includes the objects of primitive types(e.g. Integer, Long). It does not matter if an object was created and assigned to a
     local variable, or created as a member variable of another object.
  
  2. Object's member variables, no matter it is a primitive type or it is a reference to an object.
  3. Static class variables
  
  Objects on the heap can be accessed by all threads that have a reference to this object.
  
#### Example

  ```Java
  public class MyRunnable implements Runnable() {

    public void run() {
        methodOne();
    }

    public void methodOne() {
        int localVariable1 = 45;

        MySharedObject localVariable2 =
            MySharedObject.sharedInstance;

        //... do more with local variables.

        methodTwo();
    }

    public void methodTwo() {
        Integer localVariable1 = new Integer(99);

        //... do more with local variable.
    }
  }
  ```
  
  ```Java
  public class MySharedObject {

    //static variable pointing to instance of MySharedObject

    public static final MySharedObject sharedInstance =
        new MySharedObject();


    //member variables pointing to two objects on the heap

    public Integer object2 = new Integer(22);
    public Integer object4 = new Integer(44);

    public long member1 = 12345;
    public long member2 = 67890;
  }
  ```
  
   Each thread executing `methodOne()` will create its own copy of `localVariable1` and `localVariable1` on their respective thread stacks.
  
  `localVariable1` only live on each thread's thread stack. One thread cannot see changes another thread makes to its copy of
  `localVariable1`.  
  
  Each thread executing `methodOne()` will also create their own copy of `localVariable2`.
  However, the two different copies of `localVariable2` both pointing to the same object on the heap because they are object referenced 
  by a static variable. There is only one `MySharedObject` on the heap. 
  
  The two Integer objects of `MySharedObject` are stroed on heap as well as the two primitive variables `member1` and `member2`.
  
  `methodTwo()` create a local variable named `localVariable1` which is an object reference to an `Integer` object.
  The `localVariable1` reference will be stored in own thread stack. 
  While the two `Integer`(if there are two threads executing methodTwo(), then there are two Integer instance) objects 
  will be stored on the heap.
  
### How JMM works with Main Memory

  When objects and variables can be stored in various different memory areas, this can result in two main problems:
  - The update of shared objects may not be visible to other threads as long as the cpu cache has not been flushed back to main memory
    
    To solve this problem, java `volatile` keyword can be used. It guarantee that the given variable is read directly from main memory
    and always written back to main memory when updated.
    
  - Race conditions when reading, checking and writing shared variables.
  
    Java `synchronized` can be used to solve this problem. It make sure that only one thread can enter a given code at any time.
    It also guarantee that all variables accessed inside the synchronized will be read from main memory and when the thread exits
    synchronized code, all updated variables will be flushed back to main memory, regardless of whether the variable is declared
    `volatile` or not.
   
**Note : this post is from [Jakob Jenkov](http://tutorials.jenkov.com/java-concurrency/java-memory-model.html)**
