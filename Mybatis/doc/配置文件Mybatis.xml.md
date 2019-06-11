<!-- TOC -->

- [mybatis.xml配置](#mybatisxml配置)
    - [配置结构](#配置结构)
    - [属性properties](#属性properties)
    - [设置settings](#设置settings)
    - [类型别名typeAliases](#类型别名typealiases)
    - [类型处理器typeHandlers](#类型处理器typehandlers)
    - [插件plugins](#插件plugins)
    - [环境配置environments](#环境配置environments)
        - [environment](#environment)
        - [transactionManager](#transactionmanager)
        - [dataSource](#datasource)
    - [数据库厂商标识databaseIdProvider](#数据库厂商标识databaseidprovider)
    - [映射器mappers](#映射器mappers)

<!-- /TOC -->
## mybatis.xml配置
### 配置结构
MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下：
- configuration（配置）
  - properties（属性）
  - settings（设置）
  - typeAliases（类型别名）
  - typeHandlers（类型处理器）
  - objectFactory（对象工厂）
  - plugins（插件）
  - environments（环境配置）
    - environment（环境变量）
    - transactionManager（事务管理器）
    - dataSource（数据源）
  - databaseIdProvider（数据库厂商标识）
  - mappers（映射器）

注意这些设置在mybatis.xml中应该按照顺序进行配置。

刚开始学习，先学习几个比较常用的配置。其他之后用到了之后再补充。

### 属性properties
mybatis可以使用properties来引入外部properties配置文件内容。

最经常使用的就是进行数据库连接的配置。之前我们都是把数据库连接信息直接写在了mybatis.xml里面,但是这样不便于修改。我们可以直接把信息放到一个properties文件中，并在mybatis中引入，之后修改时，直接修改properties文件即可。

在src下新建mysql.properties文件
```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/testmybatis?useSSL=false
username=root
password=123456
```

在mybatis.xml中引入
```xml
<properties resource="mysql.properties"></properties>
```

之后直接写死的mysql配置信息就可以直接替换成如下形式。
```xml
<property name="driver" value="${driver}"/>
<property name="url" value="${url}"/>
<property name="username" value="${username}"/>
<property name="password" value="${password}"/>
```

### 设置settings
MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。所有的设置信息都在settings标签下。name和value用到了直接到官方文档中去找
```xml
   <settings>
        <setting name="" value=""/>
        <setting name="" value=""/>
    </settings> 
```


比较常用的两个：
**设置开启log4j日志信息**
```xml
<setting name="logImpl" value="LOG4J"/>
```
Mybatis默认是支持日志输出,但是没有log4j的配置文件，所以需要在src下添加log4j的配置文件
```properties
# Global logging configuration
log4j.rootLogger=ERROR, stdout
# MyBatis logging configuration...
log4j.logger.com.company.mapper=TRACE
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

**设置开启自动驼峰命名规则**
从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射
```xml
<setting name="mapUnderscoreToCamelCase" value="true"/>
```
### 类型别名typeAliases
类型别名是为 Java 类型设置一个短的名字。 它只和 XML 配置有关，存在的意义仅在于用来减少类完全限定名的冗余。

配置后可以在任何地方需要使用com.company.pojo.Student类的位置处。

typeAlias下面有两个标签：
typeAlias可以为某个具体的类指定别名
```xml
    <typeAliases>
        <typeAlias type="Student" alias="com.company.pojo.Student"></typeAlias>
    </typeAliases>

```
package可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，这时候直接使用类名作为别名(不区分大小写)。
```xml
    <typeAliases>
        <package name="com.company.pojo"/>
    </typeAliases>
```
### 类型处理器typeHandlers
用来进行数据库的数据类型和java的数据类型进行映射。比如数据库中的varchar和java类型中的String类型进行映射。


### 插件plugins
MyBatis 允许你在已映射语句执行过程中的某一点进行拦截调用。默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：

- **Executor** (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)
- **ParameterHandler** (getParameterObject, setParameters)
- **ResultSetHandler** (handleResultSets, handleOutputParameters)
- **StatementHandler** (prepare, parameterize, batch, update, query)

### 环境配置environments
MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中。例如，开发、测试和生产环境需要有不同的配置。

注意：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。

#### environment
用来设置一个环境

比如下面的xml中配置了两个环境mysql和test，使用时使用的是mysql环境。
```xml
    <environments default="mysql">  <!--default指明要使用哪个环境-->
        <environment id="mysql"> <!--id用来唯一标识一个环境-->
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
        <environment id="test"> <!--没有配置，理解即可-->
            <transactionManager type=""></transactionManager>
            <dataSource type=""></dataSource>
        </environment>
    </environments>
```

注意到里面的transactionManager和dataSource标签，每个环境必须要有这两个标签。

#### transactionManager
transactionManager是事务管理器在MyBatis 中有两种类型（也就是 type=”[JDBC|MANAGED]”）：

- JDBC 这个配置就是直接使用了 JDBC 的提交和回滚设置，它依赖于从数据源得到的连接来管理事务作用域。

- MANAGED 这个配置几乎没做什么。它从来不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如 JEE 应用服务器的上下文）

#### dataSource
dataSource 元素使用标准的 JDBC 数据源接口来配置 JDBC 连接对象的资源。Mybatis中有三种内建的数据源类型（也就是 type=”[UNPOOLED|POOLED|JNDI]”）
- UNPOOLED：每次被请求时打开和关闭连接
- POOLED：使用数据库连接池
- JNDI：能在如 EJB 或应用服务器这类容器中使用

### 数据库厂商标识databaseIdProvider
MyBatis 可以根据不同的数据库厂商执行不同的语句，是Mybatis在可移植性方面的支持。

为支持多厂商特性只要像下面这样在 mybatis-config.xml 文件中加入 databaseIdProvider 即可：

```xml
<databaseIdProvider type="DB_VENDOR" />
```
当然也可以给不同数据库厂商设置别名
```xml
<databaseIdProvider type="DB_VENDOR">
  <property name="MySQL" value="mysql"/>
  <property name="SQL Server" value="sqlserver"/>
  <property name="DB2" value="db2"/>
  <property name="Oracle" value="oracle" />
</databaseIdProvider>
```

之后直接在sql语句中指明databaseId即可
```sql
    <select id="selAll" resultType="Student" databaseId="mysql">
        select * from studentinfo
    </select>
```
### 映射器mappers
告诉 MyBatis 到哪里去找映射文件

mapper可以为一个Mapper接口配置映射
```xml
<mappers>
  <mapper class="com.company.mapper.StudentMapper"/>
  <mapper class="com.company.mapper.TeacherMapper"/>
</mappers>
```

也可以将包内的映射器接口实现全部注册为映射器
```xml
<mappers>
  <package name="com.company.mapper"/>
</mappers>
```