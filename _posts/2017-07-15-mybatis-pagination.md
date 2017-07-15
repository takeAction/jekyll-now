---
layout: post
title: Mybatis Pagination
categories: mybatis
---
Pagination consist of logical and physical.
1. logical means get all data first then page them in memory
2. physical represents database level subset query, e.g. mysql limit

Mybatis use logical pagination by default.  In order to use physical paging, 
developer can implement it by mybatis interceptor manually or use 3rd party.

PageHelper is a mybatis plugin which is used for paging.

Maven dependency is:
```
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.0.3</version>
</dependency>
```

In mybatis-config.xml, its configuration looks like:
```
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <property name="helperDialect" value="mysql"/>
            <property name="offsetAsPageNum" value="true"/>
            <!-- rowBoundsWithCount=true, pagehelper will execute query count first, 
            e.g. select count(0) from <your sql where clause>, and developer can get count by PageRowBounds.getTotal()-->
            <property name="rowBoundsWithCount" value="true"/>
    </plugin>
</plugins>
```

or in spring application.xml :
```
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <!-- other configuration -->
  <property name="plugins">
    <array>
      <bean class="com.github.pagehelper.PageInterceptor">
        <property name="properties">
          <!-- config params as the following -->
          <value>
            param1=value1
          </value>
        </property>
      </bean>
    </array>
  </property>
</bean>
```
**Note : In order to get query record count automatically, property     `rowBoundsWithCount`     should be set true
and     `PageRowBounds`     is required instead of     `RowBounds` .   **

For more usages, please refer to its [document](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/en/HowToUse.md)
