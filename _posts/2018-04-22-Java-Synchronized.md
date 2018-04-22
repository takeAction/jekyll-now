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
