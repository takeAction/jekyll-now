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
  
### @CachePut

  It is similar `@Cacheable`, the difference is that `@CachePut` always lets the method be executed and its result placed
  into the cache.
  
  While for `@Cacheable`, its method won't be run if cache key is the same until cache expires or gets flushed.
  
### @CacheEvict

  Remove data from cache. One can also indicate whether the eviction should occur after(the default) or before method
  execution through the `beforeInvocation` attribute.
  
### @Caching

  `@Caching` allows multiple nested `@Cacheable`, `@CachePut` and `@CacheEvict` to be used on the same method.

### @CacheConfig

  It is a class-level annotation that allows to share the cache names, the custom key generator, the custom cache manager
  and custom cache resolver.
  But it does not turn on any cache operation.
  
### @EnableCaching

  To enable above annotations work, one need to 
  - add `@EnableCaching` to one of your `@Configuration` class
  - or add `<cache:annotation-driven />` to your spring xml file
  
  > The default advice mode for processing caching annotations is "proxy" which allows for interception of calls through
  > the proxy only. Local calls within the same class cannot get intercepted thay way.
  > For a more advanced mode of interception, switch to "aspectj" mode in combination with compile-time or load-time
  > weaving.
  
  > `<cache:annotation-driven />` only looks for @Cacheable/@CachePut/@CacheEvict/@Caching on beans in the same application
  > context it is defined in. This means that, if you put `<cache:annotation-driven />` in a WebApplicationContext for a
  > DispatcherServlet, it only checks for beans in your controllers, not your services.
  
## Method visibility and cache annotation

  When using proxy, you should apply the cache annotations only to methods with `public` visibility. If you do annotate them
  on non-public method, no error is raised, but the annotated method does not exhibit the configured caching settings.
  Consider AspectJ if you want use them on non-public method.
  
  > Spring recommends that you only annotate concerete class and methods of concerete class. 
  > You certainly can place the @Cache* annotation on an interface (or an interface method), but this works only as 
  > you would expect it to if you are using interface-based proxies. The fact that Java annotations are not inherited 
  > from interfaces means that if you are using class-based proxies ( proxy-target-class="true") or the weaving-based 
  > aspect ( mode="aspectj"), then the caching settings are not recognized by the proxying and weaving infrastructure, 
  > and the object will not be wrapped in a caching proxy, which would be decidedly bad.
  
  > In proxy mode (which is the default), only external method calls coming in through the proxy are intercepted. 
  > This means that self-invocation, in effect, a method within the target object calling another method of 
  > the target object, will not lead to an actual caching at runtime even if the invoked method is marked with 
  > @Cacheable - considering using the aspectj mode in this case. Also, the proxy must be fully initialized to 
  > provide the expected behaviour so you should not rely on this feature in your initialization code, 
  > i.e. @PostConstruct.
