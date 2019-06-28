---
layout : post
title : MySql Master Slave
comments: true
categories : [MySQL, Java]
---

In mysql, there is a timestamp field whose value is `2019-06-20 17:24:00`.

The java bean looks like:

```java
public class SentLog {

    private Date sentDatetime;
}
```

However, after retrieve this value from DB, `System.out.println(s.getSentDatetime())` shows a different date time. 
They differ 13 or 14 hours.

In mysql, run `show variables like '%time_zone%';` shows 

```
system_time_zone	CST
time_zone	        SYSTEM
```

In java `TimeZone.class` :

> For compatibility with JDK 1.1.x, some other three-letter time zone IDs (such as "PST", "CTT", "AST") are also supported. 
> However, their use is deprecated because the same abbreviation is often used for multiple time zones 
> (for example, "CST" could be U.S. "Central Standard Time" and "China Standard Time"), and the Java platform can then only
> recognize one of them.

Therefore, when convert CST `2019-06-20 17:24:00` to `Date`, java use `US Time` to do conversion rather than `China Standard Time`.

There are some solutions:

1. Define `String sentDatetime` instead of `Date`
2. Set `default-time-zone='+8:00'` in `my.cnf`, if `timezone` tables have values in `mysql` database, 
     we can use `default-time-zone='Asia/Shanghai`
3. Add `serverTimezone=Asia/Shanghai` to your connection url string
