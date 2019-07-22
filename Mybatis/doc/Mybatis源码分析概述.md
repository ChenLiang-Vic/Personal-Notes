<!-- TOC -->

- [概述](#概述)
- [基础支持层](#基础支持层)
    - [反射模块](#反射模块)
    - [类型转换模块](#类型转换模块)
    - [日志模块](#日志模块)
    - [资源加载模块](#资源加载模块)
    - [解析器模块](#解析器模块)
    - [数据源模块](#数据源模块)
    - [事务管理模块](#事务管理模块)
    - [缓存模块](#缓存模块)
    - [Binding模块](#binding模块)
- [核心处理层](#核心处理层)
    - [配置解析](#配置解析)
    - [SQL解析和script模块](#sql解析和script模块)
    - [SQL执行](#sql执行)
    - [插件](#插件)
- [接口层](#接口层)

<!-- /TOC -->
### 概述
Mybatis整体架构分为三层,分别是基础支持层、核心处理层和接口层。如下图所示

![Mybatis整体结构](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/Mybatis/img/Mybatis%E6%95%B4%E4%BD%93%E6%9E%B6%E6%9E%84.png)

我们先简单认识下各个部分。

### 基础支持层
基础支持层包含Mybatis的基础模块，这些模块为核心处理层功能提供了良好的支撑。
#### 反射模块
对Java的原生反射进行了封装，并且对反射操作进行了一系列优化，如增加了类的元数据，提高了反射操作性能。
#### 类型转换模块
类型转换模块主要有两个功能
- 别名机制
- JDBC类型和Java类型之转换
#### 日志模块
很明显就是提供日志功能，主要是通过集成第三方日志框架
#### 资源加载模块
主要是对类加载器进行封装，确定类加载器的使用顺序，并提供了加载文件一节其他资源文件的功能。
#### 解析器模块
主要有两个功能：
- 对XPath进行封装，为Mybatis初始化时解析mybatis-config.xml配置文件以及映射配置文件提供支持
- 为处理动态SQL语句中的占位符提供支持
#### 数据源模块
Mybatis自身提供了相应的数据源实现，当然也提供了与第三方数据源集成的接口。
#### 事务管理模块
Mybatis对数据库中的事务进行了抽象，其自身提供了相应的事务接口和简单实现。

在很多时候Mybatis会与Spring框架集成，并由Spring框架管理事务。
#### 缓存模块

Mybatis中的一级缓存和二级缓存都依赖于基础支持层中的缓存模块实现。

注意Mybatis中自带的这两级缓存与Mybatis以及整个应用是运行在同一个JVM中的，共享同一块堆内存。如果这两级缓存中的数据量较大，则可能影响系统中其他功能的运行，所以当需要缓存大量数据时，优先考虑使用Redis等缓存产品。

![缓存示意图](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/Mybatis/img/%E7%BC%93%E5%AD%98%E6%A8%A1%E5%9D%97.png)
#### Binding模块
用来将用户自定义的Mapper接口与映射配置文件关联起来，系统可以通过调用自定义Mapper接口中的方法执行相应的SQL语句完成数据库操作。


### 核心处理层
核心处理层实现了Mybatis的核心处理流程，包括Mybatis初始化以及完成一次数据库操作的设计的全部流程。
#### 配置解析
在Mybatis初始化过程中，会加载mybatis-config.xml配置文件、映射配置文件以及Mapper接口中的注解消息，解析后的配置信息会形成相应的对象并保存到Configuration对象中。

之后利用该Configuration对象创建SqlSessionFactory对象。待Mybatis初始化后，开发人员可以通过初始化得到SqlSessionFactory创建SqlSession对象并完成数据库操作。
#### SQL解析和script模块
主要是来处理SQL的动态拼接。Mybatis中的scripting模块会根据用户传入的实参，解析映射文件中定义的动态SQL结点，并形成数据库可执行的SQL语句。之后会处理SQL语句SQL语句中的占位符，绑定用户传入实参。
#### SQL执行

SQL语句的执行涉及多个组件，其中比较重要的是：
- Executor
- StatementHandler
- ParameterHandler
- ResultSetHandler

Executor主要负责维护一级缓存和二级缓存，并提供事务管理的相关操作，他会将数据库相关操作委托给StatementHandler完成。

StatementHandler首先通过ParameterHandler完成SQL语句的实参绑定，然后通过java.sql.Statement对象执行SQL语句并得到结果集，最后通过ResultSetHandler完成结果集的映射，得到结果对象并返回。

![SQL执行示意图](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/Mybatis/img/SQL%E6%89%A7%E8%A1%8C.png)

#### 插件

Mybatis提供了插件接口，我们可以通过添加用户自定义插件的方式对Mybatis进行扩展。用户自定义插件也可以改变Mybatis的默认行为。

### 接口层
接口层相对简单，核心就是SqlSession接口，接口中定义了Mybatis中暴露给我们使用的API。

接口层在接收到调用请求时，会调用核心处理层的相应模块完成数据库操作。