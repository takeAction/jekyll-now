---
layout : post
title : Spring Cache Abstraction
categories : [Spring]
---

## Spring Cache Abstraction

  Spring provides cache abstraction which is a set of interfaces and annotations, used for applying caching to java methods.
  
  Main interfaces are `Cache` and `CacheManager`.
  
  Spring requires an actual cache implementation in order to store the cached data, e.g. Ehcache.
  
  The caching abstraction has no special handling of multi-threaded and multi-process
  (i.e. an application deployed on several nodes) environments as such features are handled by cache implementation.
  
### Process

  Each time a targeted method is invoked, the cache abstraction will apply a caching behaviour checking whether the method
  has been already executed for the given arguments. 
  If it has, then the cached result is returned without executing the method. If it has not, then method is executed, 
  the result cached and returned to the user.
   
## Cache Annotations

  - @Cacheable
  - @CacheEvict
  - @CachePut
  - @Caching
  - @CacheConfig
  - @EnableCaching
  
### @Cacheable

  As its name implies, it is used to indicate the methods are cacheable.
  This annotation declaration requires one or multiple names of the cache associated with the annotated method:
  `@Cacheable("books")` or `@Cacheable({"books", "publisher"})`
  
  For multiple names case, each of the caches will be checked before executing the method - if at least one cache is hit,
  then the associated value will be returned.  All the other caches that do not contain the value will be updated as well
  even though the cached method was not actually executed.
  
#### Default key generation

  Since caches are essentially key-value stores, each invocation of a cached method needs to be translated into a suitable
  key for cache access. By default, the caching abstraction uses a simple `KeyGenerator` based on the following
  algorithm:
  - if no parameter is given, return `SimpleKey.EMPTY`
  - if only one parameter is given, return that instance
  - if multiple parameters are given, return a `SimpleKey` containing all parameters
  
  This approach works well for most cases as long as parameters have natural keys and implement valid `hashCode()` 
  and `equals()`.
  
  To provide custom key generator, one needs to implement `KeyGenerator`.
  
#### key/keyGenerator attribute

  @Cacheable allows the user pick up arbitrary method arguments or their nested properties via key/keyGenerator attribute
  with the help of SpringEL to specify how the key is generated. For example,
  
  ```
  @Cacheable(cacheNames="books", key="#isbn")
  public Book findBook(ISBN isbn, boolean checkWarehouse, boolean includeUsed)
  
  @Cacheable(cacheNames="books", key="#isbn.rawNumber")
  public Book findBook(ISBN isbn, boolean checkWarehouse, boolean includeUsed)
  
  @Cacheable(cacheNames="books", key="T(someType).hash(#isbn)")
  public Book findBook(ISBN isbn, boolean checkWarehouse, boolean includeUsed)
  ```
  
  ```
  @Cacheable(cacheNames="books", keyGenerator="myKeyGenerator")
  public Book findBook(ISBN isbn, boolean checkWarehouse, boolean includeUsed)
  ```

#### Cache Resolution

  Simple cache resolver is used to retrieve the cache defined at the operation level using the configured `CacheManager`.
  
  `CacheResolver` can be used to achieve custom cache resolution.
  
  ```
  @Cacheable(cacheNames="books", cacheManager="anotherCacheManager")
  public Book findBook(ISBN isbn) {...}
  
  @Cacheable(cacheResolver="runtimeCacheResolver")
  public Book findBook(ISBN isbn) {...}
  ```
  
#### Sync cache

  `sync` attribute can be used to instruct the underlying cache provider to lock the cache entry while the value is being
  computed. As a result, only one thread will be busy computing the value while the others are blocked until the entry
  is updated in the cache.
  
  Note: some cache implementation may not support it.
  
#### Conditional caching

  ```
  @Cacheable(cacheNames="book", condition="#name.length() < 32", unless="#result.hardback")
  public Book findBook(String name)
  ```
  
  `condition` attribute takes SpringEL expression that is evaluated to either true or false. If true, the method is cached.
  `unless` expression is evaluated after method executed, above example means only paperback books will be cached.
  
