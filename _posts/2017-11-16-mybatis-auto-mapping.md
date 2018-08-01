---
layout: post
title: mybatis-auto-mapping
categories: Mybatis
---

### Auto mapping

As the name implies, when do auto mapping, mybatis will get the column name and look for a property with the same name ignoring case.

That means that if a column name ID and property name id are found, mybatis will set the id property with the ID column value.

In simple case, mybatis can auto-map the results for you and in other cases(e.g. nested collection/association) 
you need to build a result map by youself, or mix both strategies.

For instance, 

```xml
<resultMap id="rm" type="student">

</resultMap>
<select id="" resultMap="rm">
    SELECT
        ID,
        NAME
    FROM
        STUDENT
</select>
```

In this case, we can leave `<resultMap>` empty, just have the definition, then mybatis can auto map the result to java bean student.

or

```xml
<select id="" resultType="student">
    SELECT
        ID,
        NAME
    FROM
        STUDENT
</select>
```

However, for 

```xml
    <resultMap id="rm" type="teacher">
        <id ...>
        
        <collection property="students" ofType="student">
            <id ...>
            
        </collection>
    </resultMap>
    <select id="" resultMap="rm">
        ....
    </select>
```

For above config, we only can get id from java bean, because there is no `<result>` definition so their value are null.

In this case we have to define the mapping manually by default.

Mix strategy : all columns that are present in the ResultSet that have not a manual mapping will be auto-mapped, 
then manual mappings will be processed. 

In the following sample id and userName columns will be auto-mapped and hashed_password column will be mapped by hand.

```xml
<select id="selectUsers" resultMap="userResultMap">
  select
    user_id             as "id",
    user_name           as "userName",
    hashed_password
  from some_table
  where id = #{id}
</select>
<resultMap id="userResultMap" type="User">
  <result property="password" column="hashed_password"/>
</resultMap>
```

### Mapping level

There are three auto mapping levels:

- NONE, disable auto mapping, only manually mapped properties will be set

- PARTIAL, auto map results except those that have nested result mappings defined inside(join)

- FULL, auto maps everything

#### Mapping level config

Developer can set this level in mybatis config via `autoMappingBehavior` like:

```xml
<settings>
 
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="mapUnderscoreToCamelCase" value="true"/> 
</settings>
```

the default level is partial.

`mapUnderscoreToCamelCase` enables automatic mapping from database column like `BOOKING_ID` to java bean property like `bookingId`, default is false.

#### autoMapping attribute

If `autoMapping` attribute present in `<resultMap >`, mybatis will enable or disable the automapping for this result map.
This attibute overrides the global `autoMappingBehavior`, it is unset by default.

For nested result map, this attribute has no effect on an external resultMap.

### Usage

  With the help of `autoMappingBehavior` or `autoMapping`, there is no need to define many `<result>` manually in
  `<resultMap>` which consist of nested result map.  For instance,
  
```xml
    <resultMap id="rm" type="teacher" autoMappint="true">
        <id ...>
        
        <collection property="students" ofType="student" autoMapping="true">
            <id ...>
            
        </collection>
    </resultMap>
    <select id="" resultMap="rm">
        ....
    </select>
```

**Note: collection also need to define autoMapping attribute**

### Risk

--Note, full is used at risk--, take following to illustrate the risk:

```xml
<select id="selectBlog" resultMap="blogResult">
  select
    B.id,
    B.title,
    A.username,
  from Blog B left outer join Author A on B.author_id = A.id
  where B.id = #{id}
</select>
<resultMap id="blogResult" type="Blog">
  <association property="author" resultMap="authorResult"/>
</resultMap>

<resultMap id="authorResult" type="Author">
  <result property="username" column="author_username"/>
</resultMap>
```
With this result map and if level is full, then both Blog and Author will be auto-mapped, but Author and Blog have id property, and there is a column named id in the result set, so this id value will be mapped to Author id and Blog id, 
and that is not what we expect.

