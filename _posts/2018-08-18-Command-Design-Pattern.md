---
layout : post
title : Command Pattern
comments: true
categories : Design Pattern
---

### Problem

  Take remote for example, user press buttons to perform action on TV. We can do it with code like:
  
  ```java
  public class Remote {
      //....
      private TV tv;
      //...
      public turnOn() {
          //turn on tv logic e.g. tv.turnOn()
      }
  }
  ```
  
  For above example, there are some problems :
  
  1. Violate open-closed principle. If there are new action or existing action changed, developer need to change existing code
  
  2. It is code to implementation rather than interface
  
  3. Remote(invoker) and TV(receiver) are tightly couple
  
### Command Pattern

  In order to sovle these problems, command pattern comes. It looks like:
  
  ![_config.yml]({{ site.baseurl }}/images/template-pattern.png)
  
### Example

  There are some real world examples which use command pattern:
  
  - Runnable interface (java.lang.Runnable)
  
  - Invocation of Struts Action class by Action Servlet
