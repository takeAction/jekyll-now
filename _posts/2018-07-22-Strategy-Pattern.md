---
layout : post
title : Strategy Pattern
comments: true
categories : Java
---

![_config.yml]({{ site.baseurl }}/images/strategy-pattern.png)

```java
public class Context {

    public void pay(Payment payment) {
        payment.pay();
    }
}

public class Demo {
    
    public static void main (String[] args) {
    
        Payment p = new AliPay();
        Context context = new Context();
        context.pay(p);
    }
}
```
