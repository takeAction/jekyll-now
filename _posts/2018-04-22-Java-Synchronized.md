---
layout : post
title : Java Synchronized
categories : Java
---

From https://www.javamex.com/tutorials/synchronization_concurrency_synchronized1.shtml:

> Every Java object created, including every Class loaded, has an associated lock or monitor. 
> Putting code inside a synchronized block makes the compiler append instructions to acquire the lock 
> on the specified object before executing the code, and release it afterwards 
> (either because the code finishes normally or abnormally). Between acquiring the lock and releasing it, 
> a thread is said to "own" the lock. At the point of Thread A wanting to acquire the lock, 
> if Thread B already owns the it, then Thread A must wait for Thread B to release it.

Java provides the synchronized keyword that operates on the default lock of a class. This default lock is called intrinsic lock which belongs to every Java object.

### Synchronization type

  1. synchronized method, add `synchronized` keyword at method declaration
     
  2. synchronized statement
  
     ```
     method() {
       synchronized(obj) {
       ....
       }
     }
     ```
     
     A thread must hold the lock associated with the object obj before it can execute the code block. 
     The obj can be any kind of object which you want to use it as a lock(not primitive types).
     Or it can be `synchronized(XX.class)`.
     
     `public synchronized void update(){}` is the same as `public void update(){synchronized(this){}}`.
  
### Synchronization level

  1. instance level
  2. class level - synchronized on static method
  
Java synchronized keyword is re-entrant which means if a java synchronized method call another synchronized method which 
requires the same lock then the current thread which is holding lock can enter that method without acquiring the lock.

synchronized only be used for the shared object within the same jvm.
