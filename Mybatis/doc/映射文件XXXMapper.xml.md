
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

StudentMapper配置文件
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
    <!--因为是单个参数所以使用任何参数但为了便于理解使用名称进行占位-->
    <!--当然也可以使用arg0,arg1占位,但是不易阅读，最推荐的还是用名称-->
    <select id="selById" resultType="student">
        select * from studentinfo where id = #{id}
    </select>

    <!--单参数引用类型-->
    <!--使用传入对象的属性名进行占位-->
    <select id="selByIdName" resultType="student">
        select * from studentinfo where id = #{id} or name = #{name};
    </select>

    <!--多参数基本类型-->
    <!--可以使用arg0 arg1 Param1 Param2进行占位但是不易阅读-->
    <!--通过在接口上使用@Param("id") int id从而使用id name进行占位  实际是Mybatis自动给我们在底层封装了Map-->
    <select id="selByIdName2" resultType="student">
        select * from studentinfo where id = #{id} or name = #{name};
    </select>

    <!--多参数基本类型和引用类型混用-->
    <!--可以使用arg0 arg1 Param1 Param2进行占位  即id = #{Param1} or name = #{Param2.name}-->
    <!--通过在接口上使用@Param("")-->
    <select id="selByIdName3" resultType="student">
        select * from studentinfo where id = #{id} or name = #{s.name}
    </select>

    <!--多参数引用类型-->
    <!--可以使用arg0 arg1 Param1 Param2进行占位  即id = #{Param1.id} or name = #{Param2.name}-->
    <!--通过在接口上使用@Param("")-->
    <select id="selByIdName4" resultType="student">
    select * from studentinfo where id = #{s1.id} or name = #{s2.name}
    </select>
</mapper>
```
StudentMapper接口
```java
package com.company.mybatis.mapper;

import com.company.mybatis.pojo.Student;
import org.apache.ibatis.annotations.Param;

import java.util.List;

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
     * 单参数引用类型
     * 根据学生id或姓名查询学生信息
     * @param student
     * @return
     */
    List<Student> selByIdName(Student student);

    /**
     * 多参数基本类型
     * @param id
     * @param name
     * @return
     */
    List<Student> selByIdName2(@Param("id") int id,@Param("name") String name);

    /**
     * 多参数基本类型
     * @param id
     * @param student
     * @return
     */
    List<Student> selByIdName3(@Param("id") int id,@Param("s") Student student);

    /**
     * 多参数基本类型
     * @param student1
     * @param student2
     * @return
     */
    List<Student> selByIdName4(@Param("s1") Student student1,@Param("s2") Student student2);

}

```
测试类：
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
import java.util.List;

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

            //单参数引用类型
            Student stu = new Student();
            stu.setId(1);
            stu.setName("孙");
            List<Student> ls1 = sm.selByIdName(stu);
            System.out.println(ls1);

            //多参数基本类型
            List<Student> ls2 = sm.selByIdName2(1,"孙");
            System.out.println(ls2);

            //多参数基本类型引用类型混用
            Student stu1 = new Student();
            stu1.setName("孙");
            List<Student> ls3 = sm.selByIdName3(1,stu1);
            System.out.println(ls3);

            //多参数引用类型
            Student stu2 = new Student();
            stu2.setId(1);
            Student stu3 = new Student();
            stu3.setName("孙");
            List<Student> ls4 = sm.selByIdName4(stu2,stu3);
            System.out.println(ls4);

        }finally{
            session.close();
        }
    }
}
```

### 结果映射
结果映射主要有以下三种,重点掌握ResultMap
- 自动映射
- 驼峰
- resultMap

**自动映射**
注意到我们上面所写的返回类型resultType一直是Student,就是在使用自动映射。

什么是自动映射呢？
Mybatis会将

**驼峰转换**

**reseultMap**

## 单表增删改
增删改和查询基本一样,先写几个简单的示例,然后我们关注增删改所特有的特性。

简单实例
StudentMapper.xml
```xml

```
StudentMapper接口
```java

```
测试类
```java

```