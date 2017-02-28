---
layout: post
title: Hibernate cache
date: 2015-12-13 13:58
author: 2freesky
comments: true
categories: [Hibernate]
---
<a href="https://2freesky.files.wordpress.com/2015/12/hibernate-cache.png" rel="attachment wp-att-62"><img class="alignnone size-medium wp-image-62" src="https://2freesky.files.wordpress.com/2015/12/hibernate-cache.png?w=300" alt="hibernate cache" width="300" height="126" /></a>

<a href="https://2freesky.files.wordpress.com/2015/12/hibernate-cache-config.png" rel="attachment wp-att-63"><img class="alignnone size-medium wp-image-63" src="https://2freesky.files.wordpress.com/2015/12/hibernate-cache-config.png?w=300" alt="hibernate cache config" width="300" height="253" /></a>

for ehcache.xml(hibernate 4.3.10, hibernate-ehcache 4.3.10, ehcache-core-2.6.10), it looks like next :
<blockquote>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:noNamespaceSchemaLocation="ehcache.xsd" updateCheck="true"
monitoring="autodetect" dynamicConfig="true"&gt;

&lt;!-- diskStore: EHCache stores data into memory but when it starts overflowing, it start writing data
into file system. We use this property to define the location where EHCache will write the overflown
data. --&gt;
&lt;diskStore path="E:/ehcache" /&gt;

&lt;!-- defaultCache: It’s a mandatory configuration, it is used when an Object need to be cached and
there are no caching regions defined for that. --&gt;
&lt;!-- eternal is used to decide cached entities life will be eternal or not. Notice that when set to
"true", overrides timeToLive and timeToIdle so that no expiration can take place.

diskExpiryThreadIntervalSeconds sets the interval between runs of the expiry thread. Setting
diskExpiryThreadIntervalSeconds to a low value is not recommended. It can cause excessive DiskStore
locking and high CPU utilization. The default value is 120 seconds. If a cache's DiskStore has a
limited size, Elements will be evicted from the DiskStore when it exceeds this limit. The LFU
algorithm is used for these evictions. It is not configurable or changeable.

localTempSwap strategy allows the cache to overflow to disk during cache operation, providing an extra
tier for cache storage. This disk storage is temporary and is cleared after a restart. If the disk
store path is not specified, a default path is used, and the default will be auto-resolved in the case
of a conflict with another CacheManager. The TempSwap DiskStore creates a data file for each cache on
startup called "&lt;cache_name&gt;.data" --&gt;
&lt;defaultCache maxEntriesLocalHeap ="10000" eternal="false"
timeToIdleSeconds="120" timeToLiveSeconds="120" diskSpoolBufferSizeMB="30"
maxEntriesLocalDisk="10000000" diskExpiryThreadIntervalSeconds="120"
memoryStoreEvictionPolicy="LRU" statistics="true"&gt;
&lt;persistence strategy="localTempSwap" /&gt;
&lt;/defaultCache&gt;

&lt;!-- cache name=”student”: We use cache element to define the region and it’s configurations. We can
define multiple regions and their properties, while defining model beans cache properties, we can also
define region with caching strategies. The cache properties are easy to understand and clear with the
name. --&gt;
&lt;!-- timeToIdleSeconds enables cached object to be kept in as long as it is requested in periods shorter
than timeToIdleSeconds.

timeToLiveSeconds will make the cached object be invalidated after that many seconds irregardless of how
many times or when it was requested.

Let's say that timeToIdleSeconds = 3. Object will be invalidated if it hasn't been requested for 4
seconds.

If timeToLiveSeconds = 90 then the object will be removed from cache after 90 seconds even if it has
been requested few milliseconds in the 90th second of its short life. --&gt;

&lt;cache name="student" maxEntriesLocalHeap="10000" eternal="false" timeToIdleSeconds="5" timeToLiveSeconds="10"&gt;
&lt;persistence strategy="localTempSwap" /&gt;
&lt;/cache&gt;

&lt;/ehcache&gt;</blockquote>
