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

### Auto Mapping

```XML
<resultMap id="studentResultMap" type="student">
	
	</resultMap>
	<select id="selectStudent" resultMap = "studentResultMap" >
		select id, name, age from student where id = #{id};
	</select>
```

this can return a student object and its id, name, age have value, we don't need to define id/result in resultMap
 
```XML
   <resultMap id="studentResultMap" type="student">
		<id property="id" column="id" />
	</resultMap>
	<select id="selectStudent" resultMap = "studentResultMap" >
		select id, name, age from student where id = #{id};
	</select>
```

also can return a student object and its id, name, age have value, even we only define <id> in resultMap
    
column value is case insensitive, column="id" is the same as column="ID", while property value are case sensitive
 
```XML
    <resultMap id="selectTeacherResultMap" type="teacher">
		<id property="id" column="id" />
		<result property="name" column="t_name" />
		<collection property="students" ofType="student">
			<id property="id" column="s_id" />
			<result property="name" column="s_name" />
			<result property="age" column="age" />
		</collection>
	</resultMap>
	<select id="selectTeacher" resultMap="selectTeacherResultMap">
		select 
			t.id,
			t.name as t_name,
			s.id as s_id,
			s.name as s_name,
			s.age
		from
			teacher t left join student s on t.id = s.t_id
		
	</select>
```
    
here, age has to be defined under `<collection>`, otherwise student.getAge() return null even all fields of teacher and student are different, they all have to be defined in resultMap, otherwise their values are null in this case
