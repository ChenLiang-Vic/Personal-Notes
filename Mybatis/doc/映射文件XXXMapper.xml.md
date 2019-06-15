
## 概述
 我们先来简单看下之前写的StudentMapper.xml文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.company.mybatis.mapper.StudentMapper">
    <select id="selAll" resultType="student">
        select * from studentinfo
</mapper>
```
其中namespace属性有两个作用：
- 利用更长的完全限定名来将不同的mapper文件隔离开来
- 同时也实现了接口绑定

当然对于不使用动态代理的方式的话，namespace属性只要唯一标识这个Mapper文件即可，但要是使用了动态代理则需要通过设置namespace和接口名称相同实现接口绑定。

不使用动态代理的方式是Mybatis3之前的用法，所以我们只学习动态代理方式。

## 单表查询
### 传入参数

传入的参数类型可以是基本类型、pojo类或者是map类型。
参数个数可以是无参数、一个参数或者多个参数。

总结：
- 无参数
直接使用即可
- 单个参数
直接传入基本类型参数,可以使用任何参数进行占位，但是为了可阅读性使用#{名称}进行占位
- 多个参数
  - 可以在接口直接使用@Param()注解
  - 如果有对应POJO类，直接封装成POJO类，传入对象；使用#{属性名}占位
  - 如果没有对应POJO类，而且只是临时使用，可以手动封装成Map。使用#{键名}进行占位
  - 如果没有对应POJO类，但是经常使用，可以创建专门用来数据传输的对象TO(Transfer Object),然后将参数封装成TO再传输

StudentMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.company.mybatis.mapper.StudentMapper">

    <!--无参数-->
    <select id="selAll" resultType="student">
        select * from studentinfo
    </select>

    <!--单参数基本类型-->
    <!--因为是单个参数所以可以使用任何参数但为了便于理解使用名称进行占位-->
    <!--当然也可以使用arg0,arg1占位,但是不易阅读，最推荐的还是用名称-->
    <select id="selById" resultType="student">
        select * from studentinfo where id = #{id}
    </select>

    <!--多参数使用注解-->
    <!--可以不使用注解，直接使用arg0 arg1 Param1 Param2进行占位但是不易阅读-->
    <!--通过在接口上使用@Param("id") int id从而使用id name进行占位  实际是Mybatis自动给我们在底层封装了Map-->
    <select id="selByIdName1" resultType="student">
        select * from studentinfo where id = #{id} or name = #{name};
    </select>

    <select id="selByIdName2" resultType="student">
        select * from studentinfo where id = #{id} or name = #{s.name}
    </select>

    <select id="selByIdName3" resultType="student">
    select * from studentinfo where id = #{s1.id} or name = #{s2.name}
    </select>


    <!--多参数封装成POJO类-->
    <!--使用传入对象的属性名进行占位-->
    <select id="selByIdName4" resultType="student">
        select * from studentinfo where id = #{id} or name = #{name};
    </select>

    <!--多参数封装成POJO类-->
    <!--使用传入对象的属性名进行占位-->
    <select id="selByIdName5" resultType="student">
        select * from studentinfo where id = #{id} or name = #{name};
    </select>
</mapper>
```
StudentMapper接口
```java
package com.company.mybatis.mapper;

import com.company.mybatis.pojo.Student;
import org.apache.ibatis.annotations.Param;

import java.util.List;
import java.util.Map;

public interface StudentMapper {

    /**
     * 无参数  查询所有学生信息
     * @return
     */
    List<Student> selAll();

    /**
     * 单参数基本类型
     * 根据学生id查询学生信息
     * @return
     */
    Student selById(int id);

    /**
     * 多参数使用注解(基本类型)
     * @param id
     * @param name
     * @return
     */
    List<Student> selByIdName1(@Param("id") int id,@Param("name") String name);

    /**
     * 多参数使用注解(基本类型和引用类型)
     * @param id
     * @param student
     * @return
     */
    List<Student> selByIdName2(@Param("id") int id,@Param("s") Student student);

    /**
     * 多参数使用注解(引用类型)
     * @param student1
     * @param student2
     * @return
     */
    List<Student> selByIdName3(@Param("s1") Student student1,@Param("s2") Student student2);


    /**
     * 多参数封装成POJO类
     * 根据学生id或姓名查询学生信息
     * @param student
     * @return
     */
    List<Student> selByIdName4(Student student);

    /**
     * 多参数封装成Map
     * 根据学生id或姓名查询学生信息
     * @param map
     * @return
     */
    List<Student> selByIdName5(Map<String,Object> map);
}

```
测试类
```java
package com.company.mybatis.Demo;

import com.company.mybatis.mapper.StudentMapper;
import com.company.mybatis.pojo.Student;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class TestMybatis {
    public static void main(String[] args) throws IOException {
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(is);
        SqlSession session = factory.openSession();

        try{
            StudentMapper sm = session.getMapper(StudentMapper.class);
            //无参数
            List<Student> ls = sm.selAll();
            System.out.println(ls);

            //单参数基本类型
            Student student1 = sm.selById(1);
            System.out.println(student1);

            //多参数使用注解(基本类型)
            List<Student> ls1 = sm.selByIdName1(1,"孙");
            System.out.println(ls1);

            //多参数使用注解(基本类型和引用类型)
            Student stu1 = new Student();
            stu1.setName("孙");
            List<Student> ls2 = sm.selByIdName2(1,stu1);
            System.out.println(ls2);

            //多参数使用注解(引用类型)
            Student stu2 = new Student();
            stu2.setId(1);
            Student stu3 = new Student();
            stu3.setName("孙");
            List<Student> ls3 = sm.selByIdName3(stu2,stu3);
            System.out.println(ls3);

            //多参数封装成POJO类
            Student stu4 = new Student();
            stu4.setId(1);
            stu4.setName("孙");
            List<Student> ls4 = sm.selByIdName4(stu4);
            System.out.println(ls4);

            //多参数封装成Map
            Map<String,Object> map = new HashMap<>();
            map.put("id",1);
            map.put("name","孙");
            List<Student> ls5 = sm.selByIdName5(map);
            System.out.println(ls5);

        }finally{
            session.close();
        }
    }
}

```

### 结果映射
查询结果可以是int类型、POJO类型、map类型、List类型
int类型是增删改时的返回值类型。POJO类型和List类型直接指定resultType="pojo类"即可。对于Map类型会自动将列名设置为键，查询结果设置为值。 下面简单演示下返回结果为Map类型

```xml
<!--返回一个结果-->
<select id="selById" resultType="map">
    select * from studentinfo where id = #{id}
</select>
<!--返回多个结果-->
<select id="selById2" resultType="map">
    select * from studentinfo
```
接口
```java
//返回一个结果
Map<String,Object> selById(int id);
//返回多个结果
List<Map<String,Object>> selById2();
```
测试
```java
StudentMapper sm = session.getMapper(StudentMapper.class);
Map<String,Object> map = sm.selById(1);
System.out.println(map);
List<Map<String,Object>> ls = sm.selById2();
System.out.println(ls);
```


映射规则有：
- 自动映射(resultType)
- 驼峰规则(resultType)
- resultMap(通用)

**自动映射**
注意到我们的数据库中studentinfo表中的字段名和我们的Student实体类的字段名相同，这其实就是使用了Mybatis的自动映射(Auto Mapping)。

Mybatis的自动映射是将查询结果中列名与返回值类型中的实体类中的属性名相同的自动进行映射。

我们上面所讲的例子都是使用了自动映射。将查询出的列名id、name、age自动映射到Student类的id、name、age属性名。
```xml
<select id="selByIdName1" resultType="student">
        select * from studentinfo where id = #{id} or name = #{name}
    </select>
```

自动映射可以在mybatis.xml中进行设置
![AutoMapping]()

也可以通过给查出的列名添加别名
比如我们的数据库中表字段为sid,sname,sage  实体类为id,name,age

```xml
<select id="selByIdName" resultType="student">
        select
            sid as 'id',
            sname as 'name',
            sage as 'age'  
        from studentinfo where id = #{id} or name = #{name}
    </select>
```
但这两种都不常用，最常用的还是使用resultMap手动映射。

**驼峰规则**
从经典数据库列名 a_column 到经典 Java 属性名 aColumn 的自动映射。

默认是关闭，可以在mybatis.xml中配置生效
```xml
<setting name="mapUnderscoreToCamelCase" value="true"/>
```

比如我们的student表中的字段为id,t_name,age 而我们Student实体类为id,tname,age。那么如果不开启自动驼峰映射的话查出的name属性会为null。开启后即可直接映射。

**resultMap**
为了验证resultMap,我们把数据库中字段改为sid，sname，sage 实体类不变，仍为id，name，age

```xml
<resultMap id="studentMap" type="student">
    <id property="id" column="sid"></id> <!--主键字段用id-->
    <result property="name" column="sname"></result>  <!--普通字段使用result-->
    <result property="age" column="sage"></result>
</resultMap>

<!--注意只是对结果进行映射,SQL语句中的字段不能映射
不要写成select id,name,age from studentinfo where id = #{id}-->
<select id="selById" resultMap="studentMap" >
    select sid,sname,sage from studentinfo where sid = #{id}
</select>
```

resultMap标签中id用来唯一标识一个resultMap,type用来表示返回类型，可以是POJO也可以是map

## 单表增删改
增删改和查询基本上是一样的，只不过要注意对于增删改默认是不自动提交的我们需要手动commit或者rollback。

### 增加
```xml
<insert id="insStudent">
    insert into studentinfo values(default,#{name},#{age})
</insert>
```
StudentMapper接口:
```java
int insStudent(Student student);
```
不要忘记手动commit
```java
try{
    StudentMapper sm = session.getMapper(StudentMapper.class);

    Student student = new Student();
    student.setName("周");
    student.setAge(22);

    int i = sm.insStudent(student);
    System.out.println(i);
    session.commit();  //不要忘记手动提交
}finally{
    session.close();
}
```

**使用主键自增**
使用主键自增,插入时没有什么问题，主要是如果想要获得自增的主键值该怎么办？

```xml
<insert id="insStudent" useGeneratedKeys="true" keyProperty="id">
    insert into studentinfo values(default,#{name},#{age})
</insert>
```
可以看到和之前的insert语句相比增加了`useGeneratedKeys="true" keyProperty="id"`

useGeneratedKeys="true"开启判断是否是自增ID
keyProperty="id"  指定插入数据后自增ID返回时赋值给实体类的那个属性(这里是id属性)

在测试类中获得自增id
```java
StudentMapper sm = session.getMapper(StudentMapper.class);
Student student = new Student();
student.setName("王");
student.setAge(19);
int i = sm.insStudent(student);
System.out.println(student.getId());
session.commit();
```

**不使用主键自增**
mysql是有主键自增的，但是oracle数据库没有主键自增，一般是使用序列得到一个值，然后将这个值赋给id再将数据插入到数据库中。

对于这种方式可以使用<selectKey>标签获取主键值，这种方式适用于不提供主键自增的数据库，也适用于提供主键自增的数据库。

```xml
    </insert><insert id="insStudent">
        insert into studentinfo values(null,#{name},#{age})
        <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
            SELECT LAST_INSERT_ID()
        </selectKey>
    </insert>
```
可以看到与原来相比多了<selectKey>标签，其中keyProperty和keyColumn用来配置id列返回到哪个属性，order用来表示返回执行前或 执行后的id值。对于mysql来说是先插入再主键自增所以是AFTER,对于Oracle来说是先获得一个序列值再插入所以是BEFORE


### 修改
```xml
    <update id="upStudent">
        update studentinfo set name = #{name} where id = #{id}
    </update>
```
```java
int upStudent(Student student);
```
### 删除

```xml
    <delete id="delStudent">
        delete from studentinfo where id = #{id}
    </delete>
```
```java
int delStudent(int id);
```

## 注解方式进行增删改查
除了使用xml方式配置SQL语句外，我们也可以使用注解的方式配置SQL语句。

Mybatis注解的方式就是将SQL语句直接写在接口上。
- 优点：对于比较简单的SQL语句效率较高
- 缺点：当SQL语句变化是需要重新编译代码

一般情况下不使用注解的方式。这里只做简单介绍。

StudentMapper接口：
```java
import com.company.pojo.Student;
import org.apache.ibatis.annotations.*;


public interface StudentMapper {
    //查询@Select
    // 注意""可以写好后再换行,IDEA会自动加""
    @Select({"select * " +
            "from studentinfo " +
            "where id = #{id}"})  
    Student selById(int id);
    
    //使用resultMap手动映射
    //id用来唯一标识一个映射,可以用来继承映射
    //value表示具体的映射，其中的id = true 表明是主键
    @Results(id = "resultmap1",value = {
            @Result(property = "id",column = "id", id = true),
            @Result(property = "name",column = "name"),
            @Result(property = "age",column = "age")
    })
    @Select({"select * " +
            "from studentinfo " +
            "where id = #{id}"})
    Student selById2(int id);
    
    //插入@Insert
    @Insert({"insert into studentinfo" +
            " values(default,#{name},#{age})"})
    int insStudent(Student student);

    //返回自增主键
    @Insert({"insert into studentinfo" +
            " values(default,#{name},#{age})"})
    @Options(useGeneratedKeys = true,keyProperty = "id")
    int insStudent2(Student student);

    //返回非自增主键
    //before = false等同于AFTER
    @Insert({"insert into studentinfo" +
            " values(default,#{name},#{age})"})
    @SelectKey(statement = "SELECT LAST_INSERT_ID()",
                keyProperty = "id",
                resultType = int.class,
                before = false)
    int insStudent3(Student student);
    
    //修改@Update()
    @Update({"update studentinfo" +
            "set name =#{name}," +
            "age = #{age}" +
            "where id = #{id}"})
    int upStudent(Student student);
    //删除@Delete()
    
    @Delete({"delete from studentinfo" +
            "where id = #{id}"})
    int delStudent(int id);
}

```