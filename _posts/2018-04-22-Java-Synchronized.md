---
layout : post
title : Java Synchronized
categories : Java
---

## Multithreading

  A single cpu with single core only can handle one thread/process at a time, the OS will switch different processes/threads
  to support multi process/threads such that they look like running concurrently.
  
  Multi cpu or one cpu with multi cores can run multi processes/threads simultanously at a time on different cores.
  
## Synchronization

  1. Explicit lock
  2. Implicit lock(Intrinsic Lock)
  
### Explicit Lock

  The implementation is `Lock`.

```Java
class Clazz {

    private Lock lock = new ReentrantLock();
 
    public void test() {
    
        lock.lock();
 
        try {
 
            .....
 
        } finally {
            lock.unlock(); 
        }
    }
}
```

  The `ReentrantLock` allows a thread to acquire a lock it already owns multiple times recursively. For example,
  
  ```
  public void test1(){
      lock.lock();
 
        try {
 
            .....
            test2()
 
        } finally {
            lock.unlock(); 
        }
  }
  
  public void test2(){
      lock.lock();
 
        try {
 
            .....
 
        } finally {
            lock.unlock(); 
        }
  }
  ```
  
  When a thread is executing `test1()`, it get the lock, and it calls `test2()` which contains same lock, 
  because it also acquire it, hence it can run `test2()`. 
  
  The number of times that a thread acquires a lock is stored in a hold count variable. 
  When the thread acquires the lock, the hold count is increased by 1, and when it releases the lock, hold count is 
  decreased by 1. The lock is completely relinquished if hold count is 0. So there must be a call to unlock() for every call 
  to lock().
  
  In example above, when the current thread acquires the lock in the test1 method, hold count is 1; 
  and when it acquires the lock in the test2 method, hold count is 2. 
  When the thread releases the lock in the test2 method, hold count is 1. 
  And when the thread releases the lock in the test1 method, hold count is 0.
  
#### Lock Condition

  `Condition cond = lock.newCondition();`
  
  If condition has not been met, we can tell the current thread to wait by invoking `cond.await();`
  
  This causes the current thread blocks and waits, which means the current thread gives up the lock so other threads have 
  chance. The current thread blocks until another thread calls:
  `cond.signal();` or `cond.signalAll();`
  
  http://www.codejava.net/java-core/concurrency/java-synchronization-tutorial-part-2-using-lock-and-condition-objects:
  > The difference between signal() and signalAll() is that the signal() method wakes up only one thread among the waiting 
  > ones. Which thread is chosen depends on the thread scheduler, which means there’s no guarantee that the current thread 
  > will wake up if one thread invokes signal().
  > 
  > And the signalAll() method wakes up all threads which are currently waiting. Note that it’s up to the thread scheduler 
  > decides which thread takes the turn. All threads awake but only one is granted access to the lock. 
  > That also means there’s no guarantee that the current thread can acquire the lock again though it is waken up. 
  > If it is the case, the thread continues blocking and waiting for other chances, until it gets the locks and exits the 
  > method.
   
### Implicit Lock

  A piece of code marked with `synchronized` is used to avoid race conditions in a multi-threaded environment in Java.
  
  Internally java use a monitor also known as monitor lock or intrinsic lock to provide synchronization. 
  
  These monitors are bound to an object, thus only one thread can execute these code for same lock object at any given time.
   

From https://www.javamex.com/tutorials/synchronization_concurrency_synchronized1.shtml:

> Every Java object created, including every Class loaded, has an associated lock or monitor. 
> Putting code inside a synchronized block makes the compiler append instructions to acquire the lock 
> on the specified object before executing the code, and release it afterwards 
> (either because the code finishes normally or abnormally). Between acquiring the lock and releasing it, 
> a thread is said to "own" the lock. At the point of Thread A wanting to acquire the lock, 
> if Thread B already owns the it, then Thread A must wait for Thread B to release it.

Java provides the synchronized keyword that operates on the default lock of a class. This default lock is called intrinsic lock which belongs to every Java object.

#### Lock Condition

  `wait()`, `notify()` and `notifyAll()` are the same as `await()`, `signal()` and `signalAll()` of `Lock` object.

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

  1. instance level - synchronized method
  2. class level - synchronized on static method, `public static synchronized void update(){}` is equivalent to 
     `public static void update(){synchronized(xx.class){}}`
     
   So when a thread is executing a synchronized static method, it also blocks access to all other synchronized static methods. The synchronized non-static methods are still executable by other threads. It’s because synchronized static methods and synchronized non-static methods work on different locks: class lock and instance lock.
In other words, a synchronized static method and a non-static synchronized method will not block each other. They can run at the same time.
  
Java synchronized keyword is re-entrant which means if a java synchronized method call another synchronized method which 
requires the same lock then the current thread which is holding lock can enter that method without acquiring the lock.

synchronized only be used for the shared object within the same jvm.

https://wiki.sei.cmu.edu/confluence/display/java/LCK01-J.+Do+not+synchronize+on+objects+that+may+be+reused
