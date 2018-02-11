---
layout : post
title : Spring 4 with Ehcache
categories : [Spring, Cache]
---

### Spring Cache Abstraction

  Spring provides cache abstraction which is a set of interfaces and annotations, used for applying caching to java methods.
  
  Main interfaces are `Cache` and `CacheManager`.
  
  Spring requires an actual cache implementation in order to store the cached data, e.g. Ehcache.
  
  The caching abstraction has no special handling of multi-threaded and multi-process
  (i.e. an application deployed on several nodes) environments as such features are handled by cache implementation.
  
#### Process

  Each time a targeted method is invoked, the cache abstraction will apply a caching behaviour checking whether the method
  has been already executed for the given arguments. 
  If it has, then the cached result is returned without executing the method. If it has not, then method is executed, 
  the result cached and returned to the user.
   
### Cache Annotations

  - @Cacheable
  - @CacheEvict
  - @CachePut
  - @Caching
  - @CacheConfig
  - @EnableCaching
