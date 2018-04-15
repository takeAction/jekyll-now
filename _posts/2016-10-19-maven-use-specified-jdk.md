---
layout: post
title: maven use specified jdk
categories: [maven]
---

Currently, maven uses jdk 1.5 by default, that is even we specify JDK and compiler in eclipse, if we check out/create maven project, or update maven project or clean install, it will use jdk 1.5 rather than eclipse settings.  
This is not convenient and has error sometimes if we use higher jdk.

In order to use specified jdk, we can add following to maven settings.xml such that it uses jdk 1.7:

```
<profile>
<id>jdk-1.7</id>
<activation>
<activeByDefault>true</activeByDefault>
<jdk>1.7</jdk>
</activation>
<properties>
<maven.compiler.source>1.7</maven.compiler.source>
<maven.compiler.target>1.7</maven.compiler.target>
<maven.compiler.compilerVersion>1.7</maven.compiler.compilerVersion>
</properties>
</profile>
```
