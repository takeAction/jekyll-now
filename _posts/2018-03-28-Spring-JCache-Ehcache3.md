---
layout : post
title : Spring JCache Ehcache3
categories : Cache
---

### JCache

  JCache(a.k.a JSR-107) specification defines the standard caching api for java.
  
  Ehcache 3 offers an implementation that is fully compliant with the JCache specification.
  
  Spring provide support for two sets of annotation that can be used to implement caching - original spring annotations 
  from spring 3.1+ and the new JSR-107 annotations as from 4.1+.
  
### Code

#### Spring context

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

	<context:component-scan base-package="example.my.action,example.ehcache.web" />
	 
	<bean id="jCacheManager" class="org.springframework.cache.jcache.JCacheManagerFactoryBean">
		<property name="cacheManagerUri" value="classpath:ehcache-jcache.xml" />
	</bean>
	
	<bean id="myCacheManager" class="org.springframework.cache.jcache.JCacheCacheManager">
		<property name="cacheManager" ref="jCacheManager" />
	</bean>
	
</beans>
```

#### Ehcache config

```XML
<?xml version="1.0" encoding="UTF-8"?>
<config 
	xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
	xmlns:jsr107='http://www.ehcache.org/v3/jsr107'
	xmlns='http://www.ehcache.org/v3'
	xsi:schemaLocation='http://www.ehcache.org/v3 http://www.ehcache.org/schema/ehcache-core.xsd
	http://www.ehcache.org/v3/jsr107 http://www.ehcache.org/schema/ehcache-107-ext.xsd'>
	
	<service>
		<jsr107:defaults enable-management="false" enable-statistics="true" />
	</service>
	
	<cache alias="myCache">
		<resources>
			<heap unit="entries">2000</heap>
		</resources>
	</cache>

</config>
```

#### Service code

```
@EnableCaching
@Service
public class JCacheEhcacheService {
	
	private static int value = 0;
	
	@Cacheable("myCache")
	public int testJcache() {
		
		return value++;
	}

}
```

