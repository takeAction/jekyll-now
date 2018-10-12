---
layout : post
title : Facade Pattern
comments: true
categories : Design Pattern
---

According to GoF Facade design pattern is:

> Provide a unified interface to a set of interfaces in a subsystem. 
> Facade Pattern defines a higher-level interface that makes the subsystem easier to use.

In other words, facade design pattern is more like a helper for client applications, it provides a clearer and easier way
to interact with subsystem without writing complicated code/logic at client code. 

It hides the complexities of subsystem but doesn’t hide subsystem interfaces from the client. 
Whether to use Facade or not is decided on client code.

Facade design pattern should be applied for similar kind of interfaces, 
its purpose is to provide a single interface rather than multiple interfaces that does the similar kind of jobs.

Take computer startup for example

```java
public class CPU {
    public void processData() { }
}
 
public class Memory {
    public void load() { }
}
 
public class HardDrive {
    public void readData() { }
}
 
public class ComputerFacade {

    private CPU cpu;
    private Memory memory;
    private HardDrive hardDrive;
 
    public ComputerFacade() {
        this.cpu = new CPU();
        this.memory = new Memory();
        this.hardDrive = new HardDrive();
    }
 
    public void run() {
        cpu.processData();
        memory.load();
        hardDrive.readData();
    }
}
 
public class Demo {

    public static void main(String[] args) {
        Computer computer = new Computer();
        computer.run();
    }
}
```

**Note** : the above example is from [this](https://www.programcreek.com/2013/02/java-design-pattern-facade/) 

JDBC Driver Manager class to get the database connection is another example of facade design pattern.
