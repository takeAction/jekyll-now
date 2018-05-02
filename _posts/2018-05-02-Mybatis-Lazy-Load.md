---
layout : post
title : Mybatis Lazy Load
categories : Mybatis
---

### Mybatis config

In mybatis config file, there are three elements about lazy fetch:

```XML
<settings> 
  <setting name="lazyLoadingEnabled" value="true"/> 
  <setting name="aggressiveLazyLoading" value="false"/>
  <setting name="lazyLoadTriggerMethods"
    value="equals,clone,hashCode,toString"/>
</settings>
```

1. `lazyLoadingEnabled`. Globally enables or disables lazy loading. Default value is false.
   
   This value can be override by `fetchType`, its valid values are `lazy` and `eager`
   e.g. `<association ... fetchType="lazy" />`
   
2. `aggressiveLazyLoading`. When enabled, any method call will load all the lazy properties of the object. 
   Otherwise, each property is loaded on demand. 
   
   **This value should be set false**.
   
   Default value is false. **However its default is true in <=3.4.1**
   
3. `lazyLoadTriggerMethods`. Specifies which Object's methods trigger a lazy load.
   Default value is : equals,clone,hashCode,toString.
   
   That means equals(),clone(),hashCode(),toString() of any filed will trigger lazy load.
   
4. Both backend and frontend can trigger lazy load when corresponding field is accessed. 

   For example, there is object `Teacher`, its field `address` is defined as lazy load, then when `teacher.getAddress()`
   or `teacher.address`(such as in struts program, define `${teacher.address}` in jsp page) is called, the lazy load will run.
   
5. Because default value of `lazyLoadTriggerMethods` is `equals,clone,hashCode,toString`, you cannot make sure all of the code will
   not call these methods, especially for 3rd framework/utility/implementation. 
   
   For instance, there is object `Teacher`, its field `address` is defined as lazy load. 
   In a struts program, if developer use `${teacher.id}` in jsp page, then it will trigger lazy load even there is no call about `address`.
   
   Thus, `lazyLoadTriggerMethods` should be set empty like `<setting name="lazyLoadTriggerMethods" value=""/>`
   
### Conclusion

  In order to implement lazy load in mybatis, configuration should be:
  
  ```XML
  <settings>
		<setting name="aggressiveLazyLoading" value="false" /><!-- this has to be set as false if mybatis ≤3.4.1 -->
		<setting name="lazyLoadTriggerMethods" value=""/>
	</settings>
  ```
  
  and `fetchType="lazy"` or `lazyLoadingEnabled` is true.
