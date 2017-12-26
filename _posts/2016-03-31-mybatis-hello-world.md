---
layout: post
title: mybatis hello world
categories: [mybatis]
---

mybatis : 3.3.1

jdk     : 1.7

mysql   : 5.6

In this example, the steps as follows :

- define mybatis configuration xml file
- create POJO class which standing for table columns
- generate mapper interface which representing the operations for table, e.g. insert, for annotation usage, developer should write sql statement in mapper interface, while for xml, mapper xml configuration file needed to be created which containing sql  statements.

### Mybatis configuration xml file

```XML
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

```Java
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

```XML
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

or for annotation without mapper configuration:

```Java
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

```Java
InputStream is = Resources.getResourceAsStream("mybatis-config.xml");

SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);

SqlSession session = factory.openSession();

AuthorMapper mapper = session.getMapper(AuthorMapper.class);

mapper.deleteAuthor(10);

session.commit();
```
