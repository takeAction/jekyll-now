---
layout : post
title : Java Pass By Value
comments: true
categories : Java
---

Java is always `pass by value`, actually we can call it:

1. `pass by value` for primitive type
2. `pass by value of reference` for object

### Pass by value of reference

Take following code for example:

```java
public static void main(String[] args) {

    Student s = new Student();
    s.setName("123");
    test(s);
    System.out.println(s.getName());
}

public staic void test(Student s) {
    s.setName("abc");
}

public staic void test2(Student s1) {
    s1 = new Student();
    s1.setName("java");
}
```

`Pass by value of reference` means when `test(s)` called, java pass value of `s` not object `s`.

You can imagin that value of `s` is the pointer/address value, java pass pointer/address value to method `test(Student s)`,
that is now there are two pointers point to same student whose name is 123.

Therefore, in `test()`, change its name to 'abc' is reflected in `main()`.

However, name changed in `test2()` cannot be reflected in `main()`, because now `s1` in `test2` points to another new student,
so change it will not affect original `s`.

There is a picture from [Gevorg](https://stackoverflow.com/questions/40480/is-java-pass-by-reference-or-pass-by-value):

![_config.yml]({{ site.baseurl }}/images/pass-by-value.png)

Another [example](https://stackoverflow.com/questions/9404625/java-pass-by-reference/9404727#9404727).

### Java Integer

```java
public static void main(String[] args) {

    Integer i = 4;
    test(i);
    System.out.println(i);
}

public static void test(Integer i) {

    i = 3;
}
```

In `test()`, `i = 3` is changed to `i = Integer.valueOf(3)` internally in java,

```java
    private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }
    
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```

We can see that `i = 3` in `test()` actually points to `i = new Integer(3)`, so `i` changed to 3 in `test` will not
be reflected in `main()`.
