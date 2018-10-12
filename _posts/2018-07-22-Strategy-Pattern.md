---
layout : post
title : Strategy Pattern
comments: true
categories : Design Pattern
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

`Collections.sort(List<T> list, Comparator<? super T> c)` is an example of strategy pattern.
