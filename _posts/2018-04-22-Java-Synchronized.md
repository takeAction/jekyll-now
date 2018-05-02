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

  A piece of code marked with `synchronized` is the implementation of implicit lock.
  
  Internally java use a monitor also known as monitor lock or intrinsic lock to provide synchronization. 
  Putting code inside a synchronized block makes the compiler append instructions to acquire the lock on the specified object before 
  executing the code, and release it afterwards (either because the code finishes normally or exception). 
  
  Every java object has an associated intrinsic lock, only one thread can execute `synchronized` code for same lock object 
  at any given time.
  
  `synchronized` can only be used in same jvm.

#### Lock Condition

  `wait()`, `notify()` and `notifyAll()` are the same as `await()`, `signal()` and `signalAll()` of `Lock` object.
  
  A thread cannot call wait(), notify() or notifyAll() without holding the lock on the object the method is called on.
  
  The methods notify() and notifyAll() do not save the method calls to them in case no threads are waiting when they are called. 
  The notify signal is then just lost. Therefore, if a thread calls notify() before the thread to signal has called wait(), 
  the signal will be missed by the waiting thread. This may or may not be a problem, 
  but in some cases this may result in the waiting thread waiting forever, never waking up, because the signal to wake up was missed.

#### Synchronization type

  1. synchronized method, add `synchronized` keyword at method declaration
     
  2. synchronized statement
  
     ```
     method() {
       synchronized(obj) {
       ....
       }
     }
     ```
     
     A thread must hold the lock associated with the object before it can execute the code block. 
     The obj can be any kind of object which you want to use it as a lock(not primitive types).
     Or it can be `synchronized(XX.class)`.
     
     `public synchronized void update(){}` is the same as `public void update(){synchronized(this){}}`.
  
#### Synchronization level

  1. instance level - synchronized method
  
     For same instance, only one synchronized method can be run at one time within multiple synchronized methods.
     
     For instance, 
     ```
     public class Test{
     
         public synchronized void testA() {}
         
         public synchronized void testB() {}
         
         public void testC() {}
     }
     ```
     
     Non-synchronized method has not this restriction. 
     
     In the same instance, `testA()` and `testC()` or `testB()` and `testC()` can run concurrently. 
     While `testA()` and `testB()` cannot. Because `testA()` and `testB()` use same lock object.
     
     However, in different instance, `testA()` and `testB()` can be executed concurrently.
     
  2. class level - synchronized on static method, 
     
     `public static synchronized void update(){}` is equivalent to 
     `public static void update(){synchronized(xx.class){}}`
     
      A synchronized block in Java is synchronized on some object. 
      All synchronized blocks synchronized on the same object can only have one thread executing inside them at the same time. 
      
      A synchronized static method and a non-static synchronized method will not block each other. 
      It’s because their lock object are different: class lock and instance lock.
   
Java synchronized keyword is re-entran too.

https://wiki.sei.cmu.edu/confluence/display/java/LCK01-J.+Do+not+synchronize+on+objects+that+may+be+reused

### Happens-before

  ```
  public synchronized void increment() {
      counter++;
  }

  public synchronized int read() {
      return counter;
  }
  ```

  By synchronizing both increment and read methods we are establishing a happens-before relationship between the two 
  methods. If a given thread acquires the lock and increments the counter, any other thread that subsequently acquires the 
  lock and reads the counter will read the value that was written by the previous thread.

  If the methods were not synchronized and a given thread increments the counter, when a second thread reads the counter 
  value it is not guaranteed that the second thread will read the value that was written by the first thread, even if the 
  write happens before the read at wall clock time. This behaviour is due to the Java Memory Model specification.
