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

1. Nested result for collection

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


2. Nested select for collection
