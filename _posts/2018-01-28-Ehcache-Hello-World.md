---
layout : post
title : Ehcache Hello World
categories : [cache, Ehcache]
---

### What is Ehcache

  > Ehcache is an open source, standards-based cache that boosts performance, offloads your database, and simplifies scalability.
  
### Usage

  In order to use Ehcache, developer need to configure **CacheManager** and initialize it, then create **Cache**.
  
  This can be achieved by programmatic configuration or xml.
  
  Ehcache version is 3.4 in this post.
  
#### By XML

```XML
<config xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
	xmlns='http://www.ehcache.org/v3'
	xsi:schemaLocation="http://www.ehcache.org/v3 http://www.ehcache.org/schema/ehcache-core.xsd">

    <cache alias="foo">
        <key-type>java.lang.String</key-type>
	<value-type>java.lang.String</value-type>
	<resources>
	    <heap unit="entries">2000</heap>
	    <offheap unit="MB">500</offheap>
	</resources>
    </cache>

    <cache-template name="numberTemplate">
	<key-type>java.lang.Integer</key-type>
	<value-type>java.lang.Integer</value-type>
	<heap unit="entries">10</heap>
    </cache-template>
  
  <cache alias="bar" uses-template="numberTemplate"> 
    <key-type>java.lang.Number</key-type>
  </cache>

  <cache alias="simpleCache" uses-template="numberTemplate" />

</config>

```

  - The key and value of **cache foo** are declared as String, if not specified, default is **Object**
  - foo is declared to hold up to 2000 entries on heap
  - foo is configured up to 500 MB off-heap memory before it starts evicting
  - `<cache-template>` lets developer create an abstract configuration that further `<cache>` configurations can then **extend**
  - bar is a such a cache which using the `<cache template>` named numberTemplate and overrides its key-type to Number

```Java
    URL myUrl = Test.class.getResource("/ehcache.xml");
    XmlConfiguration xmlConfig = new XmlConfiguration(myUrl); 
    CacheManager myCacheManager = CacheManagerBuilder.newCacheManager(xmlConfig);
		
    myCacheManager.init();
		
    Calculator calculator = new Calculator();
    calculator.setCache(myCacheManager.getCache("simpleCache", Integer.class, Integer.class));
```

#### By program

```Java
public class CacheHelper {

    private CacheManager cacheManager;
    private Cache<Integer, Integer> cache;

    public CacheHelper() {
		
	cacheManager = CacheManagerBuilder.newCacheManagerBuilder().build();
    
	cacheManager.init();

	cache = cacheManager.createCache("simpleCache", CacheConfigurationBuilder
				.newCacheConfigurationBuilder(Integer.class, Integer.class, ResourcePoolsBuilder.heap(10)));
    }

  //setter and getter
}
```

```Java
public class Calculator {

    private Cache<Integer, Integer> cache;

    public int getResult(int input) {
		
        if (cache.containsKey(input)) {
			
	    return (int) cache.get(input);
        }

        System.out.println("Calculating value of " + input + " and caching result.");

        int result = input * 2;
	
        cache.put(input, result);

        return result;
    }

  .....
}
```
