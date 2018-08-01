---
layout: post
title: mybatis hello world
categories: [Mybatis]
---

mybatis : 3.3.1

jdk     : 1.7

mysql   : 5.6

In this example, the steps as follows :

- define mybatis configuration xml file
- create POJO class which standing for table columns
- generate mapper interface which representing the operations for table, e.g. insert, for annotation usage, developer should write sql statement in mapper interface, while for xml, mapper xml configuration file needed to be created which containing sql  statements.

### Mybatis configuration xml file

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!-- doctype is required and has to the same as following for configuration file -->

<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"

"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

<!-- The elements under configuration has order, e.g. if we change the order

of settings and typeAliases, then there is error tip.

-->

<settings>

<setting name="logImpl" value="SLF4J" />

</settings>

<!-- use Address to represent cn.example.mybatis.model.Address. via typeAlias-->

<typeAliases>

<typeAlias alias="Address" type="cn.example.mybatis.model.Address" />

</typeAliases>

<environments default="development">

<environment id="development">

<transactionManager type="JDBC" />

<dataSource type="POOLED">

<property name="driver" value="com.mysql.jdbc.Driver" />

<property name="url" value="jdbc:mysql://localhost:3306/test" />

<property name="username" value="root" />

<property name="password" value="" />

</dataSource>

</environment>

</environments>

<mappers>

<!-- resource for xml approach and class for annotation -->

<!-- <mapper resource="AddressMapper.xml" /> -->

<mapper class="cn.example.mybatis.annotation.AuthorMapper" />

</mappers>

</configuration>
```

### POJO class

  general pojo class
  
### mapper interface

```java
public interface AddressMapper {

public void insertAddress(String zipCode);

public List<Address> getAddressByIdList();

public Address getAddress(@Param("id") int id,

@Param("zipCode") String zipCode);

public void updateAddress();

public void updateAddressById(Address a);

public void deleteAddressById(int id);

public void insertAddress(Address a);

}
```

with mapper configuration xml file :

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!-- doctype is required and has to the same as following for mapper file -->

<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"

"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- The value of namesapce should be with package name -->

<mapper namespace="cn.example.mybatis.xml.AddressMapper">

<!-- resultType still is 'Address' even it returns a list -->

<select id="getAddressByIdList" resultType="Address">

select * from address;

</select>

<!-- multiple parameters should be use parameterType="map" -->

<select id="getAddress" parameterType="map" resultType="Address">

select * from address where id=#{id} and zipCode=#{zipCode};

</select>

<!-- parameterType is optional in this case -->

<update id="updateAddressById" parameterType="Address">

update address set zipCode=#{zipCode} where id=#{id}

</update>

<delete id="deleteAddressById">

delete from address where id=#{id}

</delete>

<!-- the 'id' of address is auto increment in DB, but developer don't need to care its value when executing insert statement, the sql just as follows. -->

<insert id="insertAddress" parameterType="Address">

insert into address(zipCode) values(#{zipCode});

</insert>

</mapper>
```

**Note:
1. parameterType is optional, resultType is required. e.g.
select * from student where id = #{id} and name = #{name}, you can pass a map which contains keys 'id' and 'name', or you can pass student java bean which has id and name fields
2. In dynamic sql, developer can use \<if>, like : 
`select * from student where <if test="id != null ">id = #{id}</if> <if test="name != null"> and name = #{name} </if>`
However if id and name are null, then this sql be `select * from student where`
or if id is null but name is not null, then sql is `select * from student where and name = ?`
bothe are wrong.
The solution is change sql to : 
`select * from student <where> <if test="id != null ">id = #{id}</if><if test="name != null"> and name=#{name}</if> </where>`
With the help of `<where>`, if id and name are null, then the sql be `select * from student` or if id is null but name is not null, then sql is `select * from student where name = ?`, mybatis will strip `and` off from `and name = #{name}`
3. `<foreach collection="list" item="i">#{i.id}</foreach>`, item is required, and if collection stands for list of custom POJO, then `#{i.id}` has to be used, if this list means List<Integer> etc, then `#{id}` is ok
**

or for annotation without mapper configuration:

```java
public interface AuthorMapper {

@Select("select * from author where id=#{id} and name=#{name}")

public Author getAuthor(@Param("id") int id,@Param("name") String name);

@Select("select * from author")

public List<Author> getAuthors();

@Insert("insert into author(name) values(#{name})")

public void insertAuthor(Author a);

@Update("update author set name=#{name} where id=#{id}")

public void updateAuthor(Author a);

@Delete("delete from author where id=#{id}")

public void deleteAuthor(int id);

}
```

### Test Class

```java
InputStream is = Resources.getResourceAsStream("mybatis-config.xml");

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);

SqlSession session = factory.openSession();

AuthorMapper mapper = session.getMapper(AuthorMapper.class);

mapper.deleteAuthor(10);

session.commit();
```

### Use sql of another xml file

  For instance, there are two sql xml files sql1.xml and sql2.xml
  
```xml
<mapper namespace="sql1">
    
    <select id="select1" resultType="student">
	....
    </select>
</mapper>
```

```xml
<mapper namespace="sql2">
    
    <select id="select2" resultType="student">
	....
    </select>
</mapper>
```

If developer want to use **select1** in sql2.xml, then he/she just use qualified name of select1, that is **sql1.select1**
