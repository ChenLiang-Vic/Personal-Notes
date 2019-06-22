
## 概念

## 创建对象的三种方式
### 构造器方式 
applicationcontext.xml
```xml
<!--无参构造器 IOC默认创建对象方式-->
<bean id="student" class="com.company.testSpring.pojo.Student"></bean>

<!--有参构造器 Student类中要有对应构造器 帮助我们创建有默认初始值的对象 相当于new Student(1,"陈",18)-->
<bean id="student2" class="com.company.testSpring.pojo.Student">
    <constructor-arg index="0" name="id" type="int" value="1"></constructor-arg>
    <constructor-arg index="1" name="name" type="String" value="陈"></constructor-arg>
    <constructor-arg index="2" name="age" type="int" value="18"></constructor-arg>
</bean>
```
测试
```java
import com.company.testSpring.pojo.Student;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class test {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationcontext.xml");

        Student student = (Student) ac.getBean("student");
        System.out.println(student);  //Student{id=0, name='null', age=0}

        Student student2 = (Student) ac.getBean("student2");
        System.out.println(student2);  //Student{id=1, name='陈', age=18}
    }
}
```
### 工厂方式

先看一下什么是工厂方式，为了演示新建了factory包，包里创建了Student类、Teacher类和StudentFactory类
```java
package com.company.testSpring.factory;

public class Student {
    private Teacher t;

    public Student(Teacher t) {
        this.t = t;
    }

    public Teacher getT() {
        return t;
    }

    public void setT(Teacher t) {
        this.t = t;
    }
}
```
```java
package com.company.testSpring.factory;

public class Teacher {
    private int id;

    public Teacher() {
    }

    public Teacher(int id) {
        this.id = id;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }
}
```
```java
package com.company.testSpring.factory;

public class StudentFactory {

    public static Student newInstance() {
        Teacher t = new Teacher(1);
        Student s = new Student(t);
        return s;
    }
}
```

工厂方式：
```xml
<!--静态工厂方式 创建对象的方法是静态的 工厂的管理交给Spring 工厂内部的对象不需要交给Spring管理-->
<bean id="sfactory" class="com.company.testSpring.factory.StudentFactory" factory-method="newInstance"></bean>

<!--注意测试动态工厂时已经把生产对象的方法改为了非静态方法-->

<!--动态工厂方式 需要先获取工厂的实例化对象 然后调用方法生产对象-->
<bean id="sfactory2" class="com.company.testSpring.factory.StudentFactory"></bean>
<bean id = "student" class="com.company.testSpring.factory.StudentFactory" factory-bean="sfactory" factory-method="newInstance"></bean>
```

```java

//静态工厂方式
Student student = (Student) ac.getBean("sfactory");
System.out.println(student);  //Student{t=Teacher{id=1}}

//注意测试前已经将静态方法改为了动态方法即去掉了static

//动态工厂方式
Student student1 = (Student) ac.getBean("student");
System.out.println(student1);  //Student{t=Teacher{id=1}}
```


### 属性注入方式

```xml
<!--属性注入方式  相当于Student s = new Student() s.setId()...-->
<bean id="student" class="com.company.testSpring.pojo.Student">
    <property name="id" value="1"></property>
    <property name="name" value="陈"></property>
    <property name="age" value="18"></property>
</bean>
```

测试：
```java
Student student = (Student) ac.getBean("student");
System.out.println(student);  //Student{id=1, name='陈', age=18}
```
## 依赖注入DI

### 构造器依赖注入

### 属性依赖注入
