---
layout : post
title : Mybatis Cache
categories : Mybatis
---

## Mybatis Cache

![_config.yml]({{ site.baseurl }}/images/mybatis-cache.png)

**Note : For spring and mybatis integration, mybatis session is tied to spring transaction**

### Second Level Cache

1. Cached object has to be implemented Serializable
2. add `<cache />` in sql xml file
3. session2 only can get cached object after session1 close or commit
4. `<select>`, `<insert>`, `<update>` and `<delete>` have `flushCache` attribute to clear cache after execute 
this statement. `<select>` has `useCache="false"` to desiable cache this select result
5. By default, `<insert>`, `<update>` and `<delete>` of **same namespace** will clear 2nd cache, developer can use `flushCach="true"` to disable this feature
6. Use LRU(Least Recently Used) to clear 2nd cache
7. `<cache readOnly="true" />` means return reference of cached object to caller, while `false` means return copy instance of cached object, defaulst is false.
8. `<cache-ref namespace="..." />` share 2nd cache config/instance

#### Custom Cache

- Implements `Cache` intertace
- `<cache type="XX.CustomCache"><property name="cacheFile" value="..." /></cache>`
- eviction strategy, readOnly etc are not applicable for custom cache

#### Mybatis and Ehcache Integration

1. add `mybatis-ehcache.jar` and `ehcache.jar`
2. add ehcache xml file
3. `<cache type="...ehcache.."/>`

Note : mybatis-ehcache-1.1.0 not support ehcache 3.X

#### 2nd Cache Risk

  Second level cache has risk at multiple tables query, please refer to [this](https://blog.csdn.net/isea533/article/details/44566257) and [this](https://www.jianshu.com/p/c553169c5921) in Chinese.
  Even developer can use `<cach-ref />` to avoid this risk, but 2nd level cache become more meaningless in this case because operation of multiple namespace will affect cache.
  
  Therefore, it is a good practice to use cache in business service layer without mybatise cache.
