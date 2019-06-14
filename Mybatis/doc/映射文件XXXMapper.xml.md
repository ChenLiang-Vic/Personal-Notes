
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
- 自动映射
- 驼峰规则
- resultMap

**自动映射**
注意到我们的数据库中studentinfo表中的字段名和我们的Student实体类的字段名相同，这其实就是使用了Mybatis的自动映射(Auto Mapping)。

Mybatis的自动映射是将查询结果中列名与返回值类型中的实体类中的属性名相同的自动进行映射。



**驼峰规则**
从经典数据库列名 a_column 到经典 Java 属性名 aColumn 的自动映射。

默认是关闭，可以在mybatis.xml中配置生效
```xml
<setting name="mapUnderscoreToCamelCase" value="true"/>
```

比如我们的student表中的字段为id,t_name,age 而我们Student实体类为id,tname,age。那么如果不开启自动驼峰映射的话查出的name属性会为null。开启后即可直接映射。

**resultMap**


## 单表增删改

