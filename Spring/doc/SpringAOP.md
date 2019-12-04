
# SpringAOP概念
在学习了SpringIOC后，我们可以非常方便的使用IOC来帮助我们创建和管理对象，实现责任链上的层与层之间对象的解耦，便于我们对对象的替换和升级。但是，很多时候我们需要的不是替换对象而是将对象中的方法进行功能的扩展。传统方式是直接修改要扩展的功能方法的源码即可。但是很多时候我们是没有源码文件的，或者说该方法不是我们自己编写的，我们希望在不修改原有方法的源码的基础上完成功能的扩展，这时候就需要用到SpringAOP了。

**切点**：要进行功能扩展的方法
**前置通知**：在切点执行之前执行的扩展代码
**后置通知**：在切点执行之后执行的扩展代码
**织入**：前置通知和后置通知和切点形成切面的过程
**切面**：前置通知和后置通知和切点形成的横向执行的面。

# Schema配置方式

我们先写一个简单的Person类
```java
package com.company.pojo;

public class Person {
    public void pDemo(){
        System.out.println("我是pDemo");
    }
}

```

接下来对Person类中的pDemo方法进行扩展，首先我们实现前置通知和后置通知

## 前置通知
```java
package com.company.advice;

import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

public class pDemoBefore implements MethodBeforeAdvice {

    /**
     * 各参数意义
     * @param method  真实方法对象(切点方法)
     * @param objects  代理方法接收的形参，使用Object数组封装了起来
     * @param o  真实对象
     *           
     * //method.invoke(o,objects);  可以用来调用真实对象的方法
     *           
     * @throws Throwable
     */
    @Override
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        //method.invoke(o,objects);  可以用来调用真实对象的方法
        System.out.println("前置通知");
    }
}
```
## 后置通知
```java
package com.company.advice;

import org.springframework.aop.AfterReturningAdvice;

import java.lang.reflect.Method;

public class pDemoAfter implements AfterReturningAdvice {

    /**
     * 各参数意义
     * @param o  真实方法返回值
     * @param method 真实方法对象
     * @param objects 代理方法接收的形参，使用Object数组封装了起来
     * @param o1  真实对象
     *            
     * @throws Throwable
     */
    @Override
    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
        System.out.println("后置通知");
    }
}

```

之后在applicationcontext.xml文件中织入前置和后置通知

applicationcontext.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--配置数据源bean-->
    <!--配置工厂bean-->
    <!--配置业务层bean-->

    <!--配置切点bean-->
    <bean id="person" class="com.company.pojo.Person"></bean>
    <!--配置前置通知bean-->
    <bean id="before" class="com.company.advice.pDemoBefore"></bean>
    <!--配置后置通知bean-->
    <bean id="after" class="com.company.advice.pDemoAfter"></bean>

    <!--声明切面-->
    <aop:config>
        <aop:pointcut id="pd" expression="execution(* com.company.pojo.Person.pDemo())"/>
        <!--前置通知-->
        <aop:advisor advice-ref="before" pointcut-ref="pd"></aop:advisor>
        <!--后置通知-->
        <aop:advisor advice-ref="after" pointcut-ref="pd"></aop:advisor>
    </aop:config>
</beans>
```


测试类

```java
package com.company.test;

import com.company.pojo.Person;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationcontext.xml");
        Person p = (Person) ac.getBean("person");
        p.pDemo();
    }
}
```

```
前置通知
我是pDemo
后置通知
```
除了前置和后置通知外还有环绕通知和异常通知
## 环绕通知
```java
package com.company.advice;


import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

/**
 * 环绕通知：
 * 效果上可以看做是将前置通知和后置通知综合到了一起，但是环绕通知可以决定是否放行真实方法。
 *
 * 注意：
 * 1.需要放行，如果不放行会造成真实方法没有执行
 * 2.环绕通知的声明在applicationcontext.xml中和配置顺序相关
 */

public class pDemoRound implements MethodInterceptor {

    /**
     * @param methodInvocation  通过调用.get...方法获得真实方法对象、形参等
     * @return  真实方法返回值
     * @throws Throwable
     */
    @Override
    public Object invoke(MethodInvocation methodInvocation) throws Throwable {
        System.out.println("环绕前");
        //放行
        Object obj = methodInvocation.proceed();
        System.out.println("环绕后");
        return obj;
    }
}

```
在applicationcontext.xml中配置织入后(方法和前置后后置相同) 进行测试

环绕通知配置在前置和后置之后的结果：
```
前置通知
环绕前
我是pDemo
环绕后
后置通知
```

环绕通知配置在前置和后置之前的结果：
```
环绕前
前置通知
我是pDemo
后置通知
环绕后
```

## 异常通知
```java
package com.company.advice;

import org.springframework.aop.ThrowsAdvice;

/**
 * 异常通知：
 * 在真实方法出现异常时调用异常通知
 */
public class pDemoThrow implements ThrowsAdvice {
    /**
     * 对catch的异常进行处理
     * @param e
     * @throws Throwable
     */
    public void afterThrowing(Exception e) throws Throwable {
        System.out.println("异常通知");
    }
}
```


##切点通配符
- 方法形参的通配符：两个点表示任意形参
`<aop:pointcut id="pd" expression="execution(* com.company.pojo.Person.pDemo(..))"/>`

- 方法名和包名：*
``<aop:pointcut id="pd" expression="execution(* com.company.*.Person.pDemo(..))"/>``所有包中的Person类中的pDemo方法


# Aspectj配置方式
