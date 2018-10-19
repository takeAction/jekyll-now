---
layout : post
title : Heap vs Stack
comments: true
categories : Java
---

In brief,

### Heap

  1. Any instance/member variables are stored in heap
  2. created java object is in heap, no matter it is created in a method or not
  
### Stack

  1. stack stores local primitive variables
  2. stores the local reference variable which points to object of heap
  3. last in first out
  
### Example

```java
public void test() {
    
    int i = 1;
    Student s = new Student();
}
```

For above code, `int i = 1` is stored in stack, 
`Student s = new Student()` will create a `Student` object in the heap, and there is local reference variable `s` in stack.

### References

Please refer to following links for more detail:

[journaldev](https://www.journaldev.com/4098/java-heap-space-vs-stack-memory)

[decodejava](https://www.decodejava.com/stack-heap-in-java.htm)

[jenkov](http://tutorials.jenkov.com/java-concurrency/java-memory-model.html)
