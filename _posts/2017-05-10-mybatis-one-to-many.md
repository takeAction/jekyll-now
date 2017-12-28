---
layout: post
title: mybatis-one-to-many
categories: mybatis
---

## One-to-Many

There are two implementations for one-to-many relationship, one is nested result, another is nested select.

Take teacher and student for example,
```
public class Teacher {

  private Integer id;
  private String name;
  private List<Student> students;
  private int age;
  
  //setter and getter
}
```
```
public class Student {
  
  private Integer id;
  private String name;
  private Integer teacherId;
  private int age;
  
  //setter and getter
}
```

### Nested result for collection

```
<resultMap id="teacherResultMap" type="teacher">

  <id property="id" column="t_id" />
  <result property="name" column="t_name" />
  
  <collection property="students" ofType="student">
    <id property="id" column="s_id" />
    <result property="name" column="s_name" />
  </collection>

</resultMap>
```
```
<select id="getTeacher" resultMap="teacherResultMap">
  select
    t.id as t_id,
    t.name as t_name,
    s.id as s_id,
    s.name as s_name
   from
    teacher t
      inner join
    student s on t.id = s.teacher_id
</select>
```
- resultMap is used in `<select>` rather than resultType
- join sql to select all teacher and their students
- in `<resultMap>`, attribute type means return which type of java bean
- in `<resultMap>`, id represents field of java bean, while column stands for column name(or alias) in select sql
- all values you want to get should be in sql statement and `<resultMap>(<id> or <result>)`, otherwise the corresponding
  value is null in result
- collection is the many of one-to-many, its property is field of one, here is students which defined in class Teacher
- ofType of collection shows java bean type, javaType can be ignore as mybatis can figure it out automatically,
  it usually is List
- for the sake of performance, `<id>` should be declared even it is not required.
- if column name are the same in sql, then we have to take a alias, otherwise, we cannot get correct result

  e.g. select t.id, s.id from ..., they are id, and if we write 
  `<id property="id" column="id" /><collection ..><id property="id" column="id" />`, 
  in this case,
  we are not able to get right one-to-many, that is if there is one teacher and two students, 
  this config will return two teachers.
  
  or there is one teacher and two students, if we don't name a alias for teacher age and student age in sql, and
  just set column="age" in `<resultMap>`, then the age of each student is equal to student1 age + student2 age.


### Nested select for collection

in this case, configuration looks like:
```
<resultMap id="tResult" type="teacher">
    <collection property="students" ofType="student" select="selectStudents4Teacher" column="id" />
</resultMap>
	
<select id="selectTeacher" resultMap="tResult">
    select * from teacher;
</select>
	
<select id="selectStudents4Teacher" resultType="student">
    select * from student where teacher_id = #{id}
</select>
  ```
  
  unlike nested result for collection, it has 2 sqls, one is used to get teachers, and another is for students
  base on teacher id. 
  
#### select

  In `<collection>`, value of select is the id of nested select.
  
  If nested select statement is in another xml file, then developer can reference it by qualified name, that is **namesapce+select id**
  
#### column
  
 > column
 > 
 > The column name from the database, or the aliased column label that holds the value that will be passed to the nested statement as 
  an input parameter. This is the same string that would normally be passed to resultSet.getString(columnName). 
 > 
 > Note: To deal with 
  composite keys, you can specify multiple column names to pass to the nested select statement by using the syntax 
  **column="{prop1=col1,prop2=col2}"**. 
  This will cause prop1 and prop2 to be set against the parameter object for the target nested select statement.
  
  If this column is not in the outer query, then nested query will not be executed.
  
  Let's have a look an example, if sql is `select id, name from teacher`, then the value of `column` in `<collection>` 
  should be **id**.
  While if sql is `select id as t_id, name from teacher`, then the value of `column` in `<collection>` should be **t_id**.
  
  **Even there is no id property in java bean Teacher, only it is in select sql, then it can be passed to select student**
  
  In select student sql, the `#{id}` can be any name, e.g. `#{teacher_id}`, it is not necessary to match value of `column`  of `<collection>`
  
#### _Note_

  ```
  <resultMap id="teacherResultMap" type="teacher" >
	
      <id property="id" column="id" />		
      
      <association property="idCard" javaType="IDCard" column="card_id" select="getCar" />
  </resultMap>
  
  <select id="getCar" resultType="IDCard">
      SELECT
          *
      FROM
          ....
     <where>
         <if test="card_id != null">
	     card_id = #{card_id}
	 </if>
	 <if test="card_no != null">
	     AND card_no = #{card_no}
	 </if>
     </where>
  ```
  
  When execute `getCar` select, error **There is no getter for property named 'card_id' in 'class java.lang.Integer'** will be thrown.
  
  This is caused by **<if test="..">**.
	
  The solution is change `column="card_id"` to **column="{card_id}"** in `association` or `collection`.
  
### Lazy load
  
   Lazy load only works in **Nested Select**.
   
   Developer can add **fetchType = 'lazy'** for `<collection select=>` or `<association select=>`to achieve it, 
   this will override the global setting:
   ```XML
   <settings>
       <setting name="lazyLoadingEnabled" value="true"/>
   </settings>
   ```
   
## One-to-One

   Mybatis use `<association property=".." javaType=".." column=".." select=".." />` to represent has one relationship.
   
   Others are similar with one-to-many.

## Auto Mapping

Auto mapping means mybatis can auto map the db columns to properties of java bean, such that there is no need for developer to define the mapping between column and property, this works if all columns of one sql belongs to one bean.

However, if in one sql, some columns belong to one bean, some columns are another bean's, then developer has to define all what you need columns in **resultMap**.

For example, 
```XML
<select id="selectCourse" resultType="course"> select id, course_name from course</select>

<resultMap id="studentResultMap" type="student">
  <association .... select="selectCourse" />	
</resultMap>
<select id="selectStudent" resultMap = "studentResultMap" >
select id, name, age from student where id = #{id};
</select>
```

for `selectStudent` sql, the columns id, name and age are properties of student, thus developr can ignore the mapping in resultMap.

But for follwing sql:
 
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
    
In above sql, columns t.id and t.name are properties of teacher bean, while s.id, s.name and s.age are properties of student bean, therefore their mappings have to be defined.

column value is case insensitive, column="id" is the same as column="ID", while property value are case sensitive
