---
layout: post
title: mybatis-auto-mapping
categories: [Mybatis]
---

### Auto mapping

That means that if a column name ID and property name id are found, mybatis will set the id property with the ID column value.

1. if column name are equals java bean field name ignore case, then auto mapping works
2. for nested collection/association, we need to add `autoMapping=true` manually
3. if column name or alias are different from java bean field name, we need to map them manually

For instance:

```xml
<select id="" resultType="student">
    SELECT
        ID,
        NAME
    FROM
        STUDENT
</select>
```

```xml
   <resultMap id="ediBookingRM" type="ediBooking" autoMapping="true">
	
		<id property="booking_id" column="booking_id" />		
		
		<collection property="cntList" ofType="ediContainer" autoMapping="true">
			<id property="id" column="CNT_ID" />					
		</collection>
		
		<collection property="goods" ofType="ediGoods" autoMapping="true">
			<id property="id" column="GOODS_ID" />					
		</collection>		
		
	</resultMap>
```

In the following sample id and userName columns will be auto-mapped and hashed_password column need to be mapped by hand.

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

**Note, full is used at risk**, take following to illustrate the risk:

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

### Note

  For following sql:
  
  ```xml
  <resultMap id="billingResultMap" type="billing" autoMapping="true">
	
	  <id property="id" column="BILLING_ID" />
      <collection property="billingChargeList" ofType="billingCharge" fetchType="lazy" column="{billing_id=billing_id,address_id=address_id}" select=listBillingCharge" />
  </resultMap>
  <sql id="getBilling" restulMap="billingResultMap">
    SELECT
			BB.ID AS BILLING_ID,
			BB.BILLING_TYPE,
			BB.BOOKING_ID,
			BB.BILLING_NO,			
			BB.BILL_TITLE,
			BB.ADDRESS,	
			BB.ADDRESS_ID,		
    ........
  </sql>
  ```
  
  **As of mybatis 3.4.0, the address_id of billing will be null, because there is address_id in column="{billing_id=billing_id,address_id=address_id}", this bug is fixed in latter version, e.g. 3.4.5**
