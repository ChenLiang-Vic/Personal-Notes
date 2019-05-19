<!-- TOC -->

- [基础](#基础)
- [数据库操作](#数据库操作)
- [单表增删改查](#单表增删改查)
    - [添加数据INSERT](#添加数据insert)
    - [删除数据DELETE](#删除数据delete)
    - [修改数据UPDATE](#修改数据update)
    - [查询数据SELECT](#查询数据select)
        - [基本查询](#基本查询)
        - [使用别名](#使用别名)
        - [去重DISTINCT和限制行数LIMIT](#去重distinct和限制行数limit)
        - [排序ORDER BY](#排序order-by)
        - [条件筛选WHERE](#条件筛选where)
        - [计算字段和拼接](#计算字段和拼接)
        - [数据处理函数](#数据处理函数)
        - [分组查询和分组筛选](#分组查询和分组筛选)
        - [SELECT语句顺序](#select语句顺序)
        - [子查询](#子查询)
- [多表联合查询](#多表联合查询)
    - [自然联结NATURAL JOIN](#自然联结natural-join)
    - [内联结(等值联结)](#内联结等值联结)
    - [外联结](#外联结)
        - [左外联结LEFT OUTER JOIN](#左外联结left-outer-join)
        - [右外联结RIGHT OUTER JOIN](#右外联结right-outer-join)
        - [全外联结FULL OUTER JOIN](#全外联结full-outer-join)
    - [自联结](#自联结)
    - [加上联结后SELECT的顺序](#加上联结后select的顺序)
- [组合查询](#组合查询)
- [二维表操作](#二维表操作)
    - [二维表创建](#二维表创建)
        - [MYSQL字段类型](#mysql字段类型)
        - [约束](#约束)
    - [二维表维护](#二维表维护)
    - [删除二维表](#删除二维表)
- [视图](#视图)
- [存储过程](#存储过程)
- [事务](#事务)
- [游标](#游标)
- [索引](#索引)
- [触发器](#触发器)
- [用户管理](#用户管理)

<!-- /TOC -->
## 基础
1. 模式定义了数据如何存储、存储什么样的数据以及数据如何分解等信息，数据库和表都有模式。

2. 主键的值不允许修改，也不允许复用（不能使用已经删除的主键值赋给新数据行的主键）。

3. SQL 语句不区分大小写，但是数据库表名、列名和值是否区分依赖于具体的 DBMS 以及配置。
4. 对于;单行SQL语句可以不写,多行SQL语句必须要写,所以不管单行多行写上;即可

SQL支持以下三种注释：
```sql
# 注释1
CREATE DATABASE test;  -- 注释2
/*注释3(一般用来注释SQL语句)*/
/*CREATE DATABASE test;*/
```
## 数据库操作
```sql
# 创建数据库
CREATE DATABASE test;
# 显示数据库 
SHOW DATABASES;
# 使用数据库
USE test;
# 删除数据库
DROP DATABASE test;
```
## 单表增删改查
### 添加数据INSERT
- 部分字段填充需要指明列名
注意:**没有指明的字段需要是该字段可以为NULL或者该字段有默认值**
```sql
INSERT INTO customers(cust_id,cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country)
VALUES('1000000006','Toy Land','123 Any Street','New York','NY','11111','USA');
```
- 全字段填充可以省略列名
```sql
INSERT INTO customers
VALUES('1000000006','Toy Land','123 Any Street','New York','NY','11111','USA',NULL,NULL);
```
- 插入检索出的数据
```sql
INSERT INTO customers(cust_id,cust_contact,cust_email,cust_name,cust_address,cust_city,
cust_state,cust_zip,cust_country)
SELECT id,contact,email,name,address,city,state,
cust_zip,cust_country
FROM custNew;
```
- 从一个表复制到另一个表
```sql
CREATE TABLE custcopy
SELECT * FROM customers;
```
### 删除数据DELETE
```sql
# 删除ID=10000000006的客户
DELETE FROM customers
WHERE cust_id = '1000000006';
```
TRUNCATE TABLE 可以清空表，也就是删除所有行。
```sql
TRUNCATE TABLE mytable;
```
### 修改数据UPDATE
```sql
# 修改顾客contact email和address
UPDATE customers
SET cust_contact = 'Sam Roberts',
cust_email = 'sam@toyland.com',
cust_address = '123 street'
WHERE cust_id = '1000000006';

# NULL来删除某列，与''不同
UPDATE customers
SET cust_email = NULL
WHERE cust_id = '1000000006';
```
注意：
1. 增删改的数据SQL语句执行完毕后，不会立马进行数据的写入。还需要手动对数据进行提交，如果数据有问题还可以回滚。但是**在MYSQL中是默认提交的**。
2. **使用更新和删除操作时一定要用 WHERE 子句，不然会把整张表的数据都破坏**。可以先用 SELECT 语句进行测试，防止错误删除。
### 查询数据SELECT
#### 基本查询
```sql
# 查询表中所有列
SELECT *
FROM products;

# 查询表中一列
SELECT prod_id
FROM products;

# 查询表中多个列
SELECT prod_id,prod_name,prod_price
FROM products;
```
#### 使用别名
别名有4种形式：
- `SELECT prod_id AS "产品编号" FROM products;`(**推荐,格式规范便于阅读**)
- `SELECT prod_id AS 产品编号 FROM products;`
- `SELECT prod_id 产品编号 FROM products;`
- `SELECT prod_id "产品编号" FROM products;`
```sql
SELECT prod_id 产品编号,prod_name AS "产品名称"
FROM products;
```
#### 去重DISTINCT和限制行数LIMIT
DISTINCT  **注意DISTINCT对之后的所有字段名有效**
```sql
SELECT DISTINCT vend_id
FROM products;
# 如果vend_id和prod_id重复相同则会去重，否则只要有一个不同则会将不同的都显示出来
SELECT DISTINCT vend_id,prod_price
FROM products;
```
LIMIT **注意第一行是从0开始的**
```sql
# 查询前5个
SELECT prod_name
FROM products
LIMIT 5; 

# 查询第2个到第8个
SELECT prod_name
FROM products
LIMIT 6 OFFSET 2; -- 2表示从第2行开始,6表示往后6行
```
#### 排序ORDER BY
```sql
# 按单列排序
SELECT prod_name
FROM products
ORDER BY prod_name;

# 可以根据不显示的列排序
SELECT prod_name
FROM products
ORDER BY prod_price;

# 按多列排序  先按第一个字段名，在案第二个字段名
SELECT prod_id,prod_name,prod_price 
FROM products
ORDER BY prod_price,prod_name;

SELECT prod_id,prod_name,prod_price 
FROM products
ORDER BY 3,2;  --先按第三列排序后按第二列排序，和上面效果一样
```
**指定排序顺序可以使用ASC(升序)和DESC(降序)，不写的话默认是ASC(升序)**
```sql

# 降序排序
SELECT prod_name
FROM products
ORDER BY prod_name DESC;

# 按第一个字段名降序，第二个字段名升序
SELECT prod_id,prod_name,prod_price 
FROM products
ORDER BY prod_price DESC,prod_name ASC;
```
#### 条件筛选WHERE
- 单条件筛选
**=　 >　　<　　>=　　<=　　<>　　IS NULL　　BETWEEN**
**注意：如果条件中的值为字符，必须使用单引号括起来**
```sql
# 商品价格低于10
SELECT prod_id,prod_price 
FROM products
WHERE prod_price < 10;

# 商品价格不等于3.49
SELECT prod_id,prod_price 
FROM products
WHERE prod_price <> 3.49;

# 商品价格在5到10之间
SELECT prod_id,prod_price 
FROM products
WHERE prod_price BETWEEN 5 AND 10;

# 客户邮件为空的id，name email
SELECT cust_id,cust_name,cust_email
FROM customers
WHERE cust_email IS NULL;

# 客户邮件不为空的id，name email
SELECT cust_id,cust_name,cust_email
FROM customers
WHERE cust_email IS NOT NULL;
```
- 组合条件筛选
AND　　OR　　IN　　NOT
注意：
1. **AND和OR一块使用时运算优先级是先AND后OR，所以为了避免出现问题，使用AND和OR时都要使用()**
2. **从下面的例子中可以看到OR和IN实现的功能相同，但是相同情况下IN比OR查询速度快，并且后面可以跟其他SQL语句**
3. **大多数DBMS允许使用NOT否定任何条件**
```sql
# AND就是与操作
SELECT prod_id,prod_price,prod_name
FROM products
WHERE vend_id = 'DLL01' AND prod_price <= 4;

# OR就是或操作
>>>>>>> e0c6f931ddb2e49a2da647a6c253f50df8b02a3b
SELECT prod_id,vend_id,prod_price
FROM products
WHERE vend_id = 'DLL01' OR vend_id = 'FNG01';

SELECT prod_id,vend_id,prod_price
FROM products
WHERE vend_id = 'DLL01' OR prod_price = 11.99;

# IN用来指定条件范围，范围中的每个条件都可以进行匹配
SELECT prod_id,vend_id,prod_price
FROM products
WHERE vend_id IN ('DLL01','FNG01');

# NOT否定其后跟的任何条件
SELECT prod_id,vend_id,prod_price
FROM products
WHERE vend_id NOT IN ('DLL01','FNG01');

SELECT prod_id,vend_id,prod_price
FROM products
WHERE NOT prod_price < 4;
```
- 通配符筛选
  - 百分号%通配符
**匹配给定位置的0个、1个或多个字符，不能匹配NULL**
  - 下划线_通配符
**匹配单个字符**
  - 方括号[]通配符(在MYSQL中无效)
**可以匹配集合内的字符，例如 [ab] 将匹配字符 a 或者 b**。用脱字符 ^ 可以对其进行否定，[^ab]也就是不匹配字符 a 或者 b。
注意：
  1. **通配符一定是和LIKE操作符一块使用**
  2. **通配符搜索只能用于文本字段（字符串），非文本数据类型字段不能使用通配符搜索**
  3. **能使用其他SQL命令替代时就用其他命令，尽可能不适用通配符，如果非要用也不要在搜索开始位置使用通配符**

```sql
# 查找以Fish开头的prod_name

SELECT prod_id,prod_name
FROM products
WHERE prod_name LIKE 'Fish%';

# 查找含有bean bag的prod_name
SELECT prod_name
FROM products
WHERE prod_name LIKE '%bean bag%'

# 匹配以F开头y结尾的prod_name
SELECT prod_name
FROM products
WHERE prod_name LIKE 'F%y'

# 匹配vend_id中的DLL01
SELECT vend_id
FROM products
WHERE vend_id LIKE 'D_L_1'
```
#### 计算字段和拼接
存储在表中的数据都不是应用程序所需要的。我们需要直接从数据库中检索出转换、计算或格式化过的数据，而不是检索出数据，然后再在客户端应用程序中重新格式化。
- 使用CONCAT()进行字段拼接
```sql
# 将vend_name和vend_country拼接起来只显示一列
SELECT CONCAT(vend_name,'(',vend_country,')')
FROM vendors;
```
上面的SQL语句中只有数据进行了拼接,字段却是CONCAT(vend_name,'(',vend_country,')')所以一般拼接后都要使用别名
```SQL
SELECT CONCAT(vend_name,'(',vend_country,')') AS '供应商信息'
FROM vendors;
```
许多数据库会使用空格把一个值填充为列宽，因此连接的结果会出现一些不必要的空格，使用TRIM()可以去除首尾空格。
```sql
SELECT CONCAT(TRIM(vend_name),'(',TRIM(vend_country),')')
FROM vendors;
```
- 执行计算
**支持+　　-　　*　/运算**
```sql
# 数量乘以单价
SELECT prod_id,quantity,item_price,quantity*item_price AS expanded_price
FROM orderitems
WHERE order_num = 20008;
```
另外,SELECT除了进行数据查询外，还可以进行计算的验证，如
```sql
SELECT 2*3;  --返回6
SELECT NOW();  --返回当前时间
```
#### 数据处理函数
各个 DBMS 的函数都是不相同的，因此不可移植，以下主要是 MySQL 的函数。
- **汇总函数** 
|函 数 |说 明|
| :---: | :---: |
| AVG() | 返回某列的平均值 |
| COUNT() | 返回某列的行数 |
| MAX() | 返回某列的最大值 |
| MIN() | 返回某列的最小值 |
| SUM() |返回某列值之和 |  

  注意：
  1. **因为汇总函数结果只有一行，所以不能和其他字段名一块使用，但可以几个汇总函数一块使用(因为都是一行)**
  2. **AVG()和COUNT()会忽略NULL行**
  3. **WHERE子句中不允许出现汇总函数**
```sql
# 商品价格平均值
SELECT AVG(prod_price) AS avg_price
FROM products;

# MAX()用来查找最大值
SELECT MAX(prod_price) AS max_price
FROM products;

# MIN()用来查找最小值
SELECT MIN(prod_price) AS max_price
FROM products;

# 可以用COUNTT(*)用来计算行数
SELECT COUNT(*) AS prod_count
FROM products;

# SUM()用来求和
SELECT SUM(quantity) AS sum_quatity
FROM orderitems;

# SUM()用来求计算式的和
SELECT SUM(quantity*item_price) AS sum_money
FROM orderitems;
```
AVG() COUNT() SUM()可以使用DISTINCT进行去重,MAX()和MIN()去重没有意义
```sql
SELECT AVG(DISTINCT prod_price) AS avg_price
FROM products;
```
- **文本处理函数**
| 函数  | 说明  |
| :---: | :---: |
|  LEFT() |  左边的字符 |
| RIGHT() | 右边的字符 |
| LOWER() | 转换为小写字符 |
| UPPER() | 转换为大写字符 |
| LTRIM() | 去除左边的空格 |
| RTRIM() | 去除右边的空格 |
| LENGTH() | 长度 |
| SOUNDEX() | 转换为语音值 |  

其中，SOUNDEX() 可以将一个字符串转换为描述其语音表示的字母数字模式。
```sql
# 查询prod_name和其名字长度
SELECT prod_name,LENGTH(prod_name)
FROM products;
```
- **日期和时间处理函数**
  - 日期格式：YYYY-MM-DD
  - 时间格式：HH:<zero-width space>MM:SS

|函 数 | 说 明|
| :---: | :---: |
| ADDDATE() | 增加一个日期（天、周等）|
| ADDTIME() | 增加一个时间（时、分等）|
| CURDATE() | 返回当前日期 |
| CURTIME() | 返回当前时间 |
| DATE() |返回日期时间的日期部分|
| DATEDIFF() |计算两个日期之差|
| DATE_ADD() |高度灵活的日期运算函数|
| DATE_FORMAT() |返回一个格式化的日期或时间串|
| DAY()| 返回一个日期的天数部分|
| DAYOFWEEK() |对于一个日期，返回对应的星期几|
| HOUR() |返回一个时间的小时部分|
| MINUTE() |返回一个时间的分钟部分|
| MONTH() |返回一个日期的月份部分|
| NOW() |返回当前日期和时间|
| SECOND() |返回一个时间的秒部分|
| TIME() |返回一个日期时间的时间部分|
| YEAR() |返回一个日期的年份部分|

```sql
# 查询当前时间
SELECT NOW();
```
- **数值处理函数**
| 函数 | 说明 |
| :---: | :---: |
| SIN() | 正弦 |
| COS() | 余弦 |
| TAN() | 正切 |
| ABS() | 绝对值 |
| SQRT() | 平方根 |
| MOD() | 余数 |
| EXP() | 指数 |
| PI() | 圆周率 |
| RAND() | 随机数 |
```sql
# 表中没有什么可以计算的，就直接验证下
SELECT ABS(-1)  -- 结果为1
```
#### 分组查询和分组筛选
- 分组查询GROUP BY
注意：
1. **使用了GROUP BY进行分组后,SELECT语句中只允许出现分组字段和多行函数**
2. **如果是多字段分组，先按第一字段进行分组，后按照第二字段进行分组，依次类推**
```sql
# 查询不同供应商提供的产品总数
SELECT vend_id,COUNT(*)
FROM products
GROUP BY vend_id;

# 查询不同供应商提供的不同价格的产品总数
SELECT vend_id,prod_price,COUNT(*)
FROM products
GROUP BY vend_id,prod_price;  -- 先按第一个字段分组，再按第二个字段分组
```
- 分组筛选HAVING
针对分组进行分组后的数据筛选，允许使用多行函数
HAVING和WHERE的区别：
1. **最核心的区别是HAVING是对分组进行筛选而WHERE是对字段进行筛选**
2. having关键字必须和group by一块使用，不能单独使用
3.  where子句不允许出现多行函数，having子句允许出现多行函数
```sql
# 查询不同供应商提供的不同价格的产品总数大于1的产品信息
SELECT vend_id,prod_price,COUNT(*) AS 'prod_num'
FROM products
GROUP BY vend_id,prod_price
HAVING prod_num>1;

# 查询不同供应商提供的产品价格大于4的并且产品总数大于1的产品信息
SELECT vend_id,prod_price,COUNT(*) AS 'prod_num'
FROM products
WHERE prod_price > 4
GROUP BY vend_id
HAVING prod_num>1;
```
#### SELECT语句顺序
```sql
SELECT 
FROM
WHERE
GROUP BY
HAVING
ORDER BY
```
#### 子查询
**当查询条件不明确时，考虑使用子查询。这里的不明确是指没有具体的数值，需要经过一次查询后才能获得的数值。**

注意：**作为子查询的SELECT语句只能查询单个列，查询多个列时会出错。**
- 查询结果只有一个数据
```sql
#查询比平均价格高的商品信息
SELECT *
FROM products
WHERE prod_price > (SELECT AVG(DISTINCT prod_price) FROM products);
```
- 查询结果为一个字段的数据
使用ANY(任意)　　ALL(所有)　　IN(任意存在)关键字
```sql
#查询产品价格高于DLL01提供的产品信息
SELECT *
FROM products
WHERE prod_price > ALL(SELECT prod_price FROM products WHERE vend_id = 'DLL01');
#查询产品价格高于任意一个DLL01提供的产品信息
SELECT *
FROM products
WHERE prod_price > ANY(SELECT prod_price FROM products WHERE vend_id = 'DLL01');
#查询产品价格与DLL01提供的产品价格相同且低于4的产品信息
SELECT *
FROM products
WHERE prod_price IN (SELECT prod_price FROM products WHERE vend_id = 'DLL01') AND prod_price<4;
```
子查询除了可以在where子句中对数据进行过滤外，还可以作为计算字段。
```sql
SELECT cust_name,cust_state,(SELECT COUNT(*) FROM orders WHERE orders.cust_id = customers.cust_id) AS orders
FROM Customers
ORDER BY cust_name;
```
## 多表联合查询
当所需要查询的数据分布在多张表上时，需要使用多表查询（联结查询）

注意：
- 如果使用**ON**或者**USING**关键字对结果进行筛选必须使用**INNER JOIN**进行表与表的连接，**其中inner可以省略**
- **外连接的outer关键字可以省略不写**
- **依然可以继续使用GROUP BY，HAVING, ORDER BY等**  

**笛卡尔积**  
笛卡尔积也称直积，两个集合X和Y的笛卡尓积表示为X × Y，第一个对象是X的成员而第二个对象是Y的所有可能有序对的其中一个成员。例如集合A={a, b}，集合B={0, 1, 2}，则两个集合的笛卡尔积为{(a, 0), (a, 1), (a, 2), (b, 0), (b, 1), (b, 2)}。

**在数据库中即为将多个表中的数据进行一一对应，所得到的结果为多表的笛卡尔积，结果数量为所有表的数量乘积。**
```sql
SELECT vend_name,prod_name
FROM vendors
CROSS JOIN products; --vendors6条记录，products9条记录，总共查询出54条记录  (CROSS可以省略)

# 简单写法，不推荐使用(SQL92)
SELECT vend_name,prod_name
FROM vendors,products; 
```
### 自然联结NATURAL JOIN
**底层先笛卡尔积，然后按照所有的<font color='red'>同名同值</font>的字段<font color='red'>自动</font>进行<font color='red'>等值筛选</font>**
```sql
#products表和vendors表有同名同值的vend_id外键
SELECT vend_name,prod_name
FROM vendors
NATURAL JOIN products; 
```
### 内联结(等值联结)
**先做表的笛卡尔积，然后筛选，筛选条件为等值筛选，和自然联结不同的是内联结可以指定筛选条件和使用的筛选字段**

- 按照部分字段筛选(两字段字段名相同) 
如果有两个或多个外键，但是只想按照部分字段进行筛选，使用**USING**关键字
```sql
SELECT vend_name,prod_name
FROM vendors
INNER JOIN products
USING(vend_id);  --这个例子不是很恰当，因为只有一个外键
```
- 按照字段名不同值相同筛选
```sql
# 使用别名(实际使用多为这样用)
SELECT v.vend_name,p.prod_name
FROM vendors AS v
INNER JOIN products AS p
ON v.vend_id = p.vend_id;
```
### 外联结
许多联结将一个表中的行与另一个表中的行相关联，但有时候需要包含没有关联行的那些行。外联结则保留了没有关联的那些行。分为左外联结，右外联结以及全外联结。
表student:
| id | name | age |
|:--:|:--:|:--:|
| 1 | Jim | 18
| 2 | Lucy | 16 |
| 3 | Lily | 16 |
| 4 | Lilei | 17 |
| 5 | Hanmeimei | 16 |

表mark:
| id | subject | grade |
|:--:|:--:|:--:|
| 1 | English | 90 |
| 1 | Math | 80 |
| 2 | English | 95 |
| 2 | Math | 70 |
| 3 | English | 70 |
| 3 | Math | 80 |
| 4 | English | 80 |
| 4 | Math | 80 |
| 8 | English | 90 |
| 8 | Math | 90 |
表info：
| id | city | district |
|:--:|:--:|:--:|
| 1 | nanjing | gulou |
| 2 | beijing | chaoyang |
| 3 | shanghai | pudong |
| 4 | hangzhou | xiaoshan |
| 5 | chengdu | wuhou |
| 6 | tianjing | hedong |
#### 左外联结LEFT OUTER JOIN
左外联结就是保留左表没有关联的行。
![左外联结](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E6%95%B0%E6%8D%AE%E5%BA%93/img/%E5%B7%A6%E5%A4%96%E8%81%94%E7%BB%93.png)
```sql
SELECT s.id,m.subject
FROM student AS s
LEFT JOIN mark AS m
on s.id=m.id;
```
结果：
```sql
+----+---------+
| id | subject |
+----+---------+
| 1 | English |
| 1 | Math |
| 2 | English |
| 2 | Math |
| 3 | English |
| 3 | Math |
| 4 | English |
| 4 | Math |
| 5 | NULL | -- student表中5号保留  直接内联结的话就不显示5号
```
#### 右外联结RIGHT OUTER JOIN
右外联结就是保留右表没有关联的行。
![右外联结](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E6%95%B0%E6%8D%AE%E5%BA%93/img/%E5%8F%B3%E5%A4%96%E8%81%94%E7%BB%93.jpg)
```SQL
SELECT s.id,m.subject
FROM student AS s
RIGHT JOIN mark AS m
on s.id=m.id;
```
结果：
```sql
+------+---------+
| id | subject |
+------+---------+
| 1 | English |
| 1 | Math |
| 2 | English |
| 2 | Math |
| 3 | English |
| 3 | Math |
| 4 | English |
| 4 | Math |
| NULL | English |
| NULL | Math |  --mark表中8号保留
+------+---------+
```
#### 全外联结FULL OUTER JOIN
全外联结就是保留两个表分别没有关联的行。
![全外联结](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E6%95%B0%E6%8D%AE%E5%BA%93/img/%E5%8F%B3%E5%A4%96%E8%81%94%E7%BB%93.jpg)
MYSQL中没有全外联结，需要使用UNION组合左外联结和右外联结结果
```sql
SELECT s.*,subject,score
FROM student s 
LEFT JOIN mark m
ON s.id=m.id 
UNION 
SELECT s.*,subject,score
FROM student s 
RIGHT JOIN mark m
ON s.id=m.id;
```
结果：
```sql
+------+-----------+------+---------+-------+
| id | name | age | subject | score |
+------+-----------+------+---------+-------+
| 1 | Jim | 18 | English | 90 |
| 1 | Jim | 18 | Math | 80 |
| 2 | Lucy | 16 | English | 95 |
| 2 | Lucy | 16 | Math | 70 |
| 3 | Lily | 16 | English | 70 |
| 3 | Lily | 16 | Math | 80 |
| 4 | Lilei | 17 | English | 80 |
| 4 | Lilei | 17 | Math | 80 |
| 5 | Hanmeimei | 16 | NULL | NULL |
| NULL | NULL | NULL | English | 90 |
| NULL | NULL | NULL | Math | 90 |
+------+-----------+------+---------+-------+
```
### 自联结

一张员工表，包含员工编号、姓名、工作、工资、上级领导，其中员工中包含上级领导。
```sql
# 查询员工姓名，工作，工资及上级领导姓名
SELECT e1.ename,e1.job,e1.sal, e2.ename
FROM emp e1
INNER JOIN emp e2
ON e1.mgr=e2.empno;
```

### 加上联结后SELECT的顺序
```sql
SELECT 内容 FROM 表名1 
INNER JOIN 表名2 
ON 连接条件
INNER JOIN 表名3 
ON 连接条件
WHERE 普通筛选条件
GROUP BY 分组
HAVING 多行函数筛选
ORDER BY 排序
```
## 组合查询
- 使用 UNION 来组合两个查询，如果第一个查询返回 M 行，第二个查询返回 N 行，那么组合查询的结果一般为 M+N 行。
- 每个查询必须包含相同的列、表达式和聚集函数。
- 默认会去除相同行，如果需要保留相同行，使用 UNION ALL。
- 只能包含一个 ORDER BY 子句，并且必须位于语句的最后。
```sql
SELECT cust_name, cust_contact, cust_email
FROM customers
WHERE cust_state IN ('IL','IN','MI')
UNION
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_name = 'Fun4All';
```
## 二维表操作
### 二维表创建
```sql
CREATE TABLE 表名(
    字段名1 字段类型 约束条件
    字段名2 字段类型 约束条件
    ......
)

CREATE TABLE student(
	stu_id INT(10) NOT NULL PRIMARY KEY,
	stu_name VARCHAR(50) NOT NULL
)
```
#### MYSQL字段类型
- 数值类型
![数值类型](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E6%95%B0%E6%8D%AE%E5%BA%93/img/%E6%95%B0%E5%80%BC%E7%B1%BB%E5%9E%8B.png)
- 字符串类型
![字符串类型](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E6%95%B0%E6%8D%AE%E5%BA%93/img/%E5%AD%97%E7%AC%A6%E7%B1%BB%E5%9E%8B.png)

  char和varchar
  1. char(n) 若存入字符数小于n，则以空格补于其后，查询之时再将空格去掉。所以char类型存储的字符串末尾不能有空格，varchar不限于此。 
  2. char(n) 固定长度，char(4)不管是存入几个字符，都将占用4个字节，varchar是存入的实际字符数+1个字节（n<=255）或2个字节(n>255)，所以varchar(4),存入3个字符将占用4个字节。 3. char类型的字符串检索速度要比varchar类型的快

  varchar和text： 
  1. varchar可指定n，text不能指定，内部存储varchar是存入的实际字符数+1个字节（n<=255）或2个字节(n>255)，text是实际字符数+2个字节。 
  2. text类型不能有默认值。 
  3. varchar可直接创建索引，text创建索引要指定前多少个字符。varchar查询速度快于text,在都创建索引的情况下，text的索引似乎不起作用。
- 时间类型
![时间类型](https://github.com/ChenLiang-Vic/Personal-notes/blob/master/%E6%95%B0%E6%8D%AE%E5%BA%93/img/%E6%97%B6%E9%97%B4%E7%B1%BB%E5%9E%8B.png)
- 二进制
布尔:bit
bit 表示1个二进制的位
bit(8) 表示8个二进制的位
性别可以定义为0,1, 而不使用male或female字符串
数据逻辑删除
某辆车在车库中停放的状态
所有基于两种状态的数据都可以使用0,1来存储.
#### 约束
约束是一种限制，它通过对表的行或列的数据做出限制，来确保表的数据的完整性、唯一性。
- **主键约束PRIMARY KEY**
  1. 主键非空唯一，一般与`auto_increment`一块使用
  2. 每个表最多只允许一个主键，建立主键约束可以在列级别创建，也可以在表级别创建。
  3. 当创建主键的约束时，系统默认会在所在的列和列组合上建立对应的唯一索引。
```sql
# 基本模式
CREATE TABLE temp (
	id INT(10) PRIMARY KEY,
	NAME VARCHAR (20)
);

# 组合模式
CREATE TABLE temp (
	id INT(10),
	name VARCHAR (20),
	pwd VARCHAR (20),
	PRIMARY KEY (id,name)
);
/*注意：组合模式不是设置了两个主键，而是几个字段组合起来作为一个主键

比如学生选课数据库系统，学生表student（sno，sname，……），课程表course（cno，cname，……），选课表sc（sno，cno，grade） 
学生表的学号sno为主键，课程表的课程号cno为主键，而选课表是以（sno，cno）才能作为主键*/

# 删除主键约束
ALTER TABLE temp DROP PRIMARY KEY;

# 添加主键约束
ALTER TABLE temp ADD PRIMARY KEY (id, NAME);

# 修改主键约束
ALTER TABLE temp MODIFY id INT PRIMARY key；
```
- **外键约束 FOREIGN KEY** 
当一张表的某个字段的值需要依赖另外一张表的某个字段的值，则使用外键约束其中主动依赖的表称为**子表**，被依赖的表称为**父表**。**外键加在子表中**

  
  作用：**当在子表中插入的数据在父表中不存在，则会自动报错**
```sql
-- 基本模式
-- 主表
CREATE TABLE temp (
	id INT PRIMARY KEY,
	name VARCHAR (20)
);

-- 副表
CREATE TABLE temp2 (
	id INT,
	name VARCHAR (20),
	classes_id INT,
	FOREIGN KEY (id) REFERENCES temp (id)
);

-- 多列外键组合，必须用表级别约束语法
-- 主表
CREATE TABLE classes (
	id INT,
	name VARCHAR (20),
	number INT,
	PRIMARY KEY (NAME, number)
);

-- 副表
CREATE TABLE student (
	id INT auto_increment PRIMARY KEY,
	name VARCHAR (20),
	classes_name VARCHAR (20),
	classes_number INT,
	/*表级别联合外键*/
	FOREIGN KEY (
		classes_name,
		classes_number
	) REFERENCES classes (NAME, number)
);

-- 删除外键约束
ALTER TABLE student DROP FOREIGN KEY student_id;

-- 增加外键约束
ALTER TABLE student ADD FOREIGN KEY (
	classes_name,
	classes_number
) REFERENCES classes (NAME, number);
```
- **非空约束 NOT NULL**
非空约束用于确保当前列的值不为空值，非空约束只能出现在表对象的列上。

Null类型特征：
所有的类型的值都可以是null，包括int、float 等数据类型
```sql
-- 创建table表，ID 为非空约束，name 为非空约束 且默认值为abc
CREATE TABLE temp (
	id INT(10) NOT NULL,
	name VARCHAR (50) NOT NULL DEFAULT 'abc',
	sex CHAR NULL
) ;
-- 增加非空约束
ALTER TABLE temp MODIFY sex VARCHAR (2) NOT NULL;

-- 取消非空约束
ALTER TABLE temp MODIFY sex VARCHAR (2) NULL;

-- 取消非空约束，增加默认值
ALTER TABLE temp MODIFY sex VARCHAR (2) DEFAULT 'abc' NULL;
```
- **唯一约束 UNIQUE**
  1. 唯一约束是指定table的列或列组合不能重复，保证数据的唯一性。
  2. 唯一约束不允许出现重复的值，但是可以为多个null。
  3. 同一个表可以有多个唯一约束，多个列组合的约束。
```sql
-- 创建表时设置，表示用户名、密码不能重复
CREATE TABLE temp (
	id INT NOT NULL,
	name VARCHAR (20),
	pwd VARCHAR (10),
	UNIQUE (name, pwd)
);

-- 添加唯一约束
ALTER TABLE temp ADD UNIQUE (name, pwd);

-- 修改唯一约束
ALTER TABLE temp MODIFY name VARCHAR (25) UNIQUE;

-- 删除约束
ALTER TABLE temp DROP INDEX name;
```
- **默认约束 DEFAULT**
```sql
-- 名字默认为abc
CREATE TABLE temp (
	id INT NOT NULL,
	name VARCHAR (255) NOT NULL DEFAULT 'abc',
	sex CHAR NULL
);
```
- 检查约束(MYSQL中暂时不支持)
### 二维表维护
- **添加新的字段**
```sql
# 添加prod_haha字段
ALTER TABLE products ADD prod_haha VARCHAR(10) NOT NULL;
```
- **修改原字段**
```sql
# 修改字段名
ALTER TABLE products CHANGE prod_haha prod_quatity VARCHAR(10);

# 修改字段类型
ALTER TABLE products MODIFY COLUMN prod_quatity VARCHAR(20);
```
- **删除原有字段**
```sql
# 删除原有字段
ALTER TABLE products DROP COLUMN prod_quatity;
```
- **修改表名**
```sql
#方式1
ALTER TABLE orders rename to orders2;
#方式2
rename table orders2 to orders;
```
### 删除二维表
```sql
DROP TABLE custcopy;
```
## 视图
视图是一个**虚表**，即视图包含的不是数据而是根据需要检索数据的查询。视图提供了一种封装SELECT语句的层次，可用来简化数据处理，重新格式化和保护基础数据。

视图的好处：
1. 重用SQL语句，对于复杂点的SQL语句将它创建为视图后可以方便的重用
2. 因为视图可以使用表的一部分而不是整个表，所以可以保护原来表中的一些重要数据。
3. 视图可以更改数据格式和表示。

```sql
CREATE VIEW myview AS
SELECT col1, col2, col3*col4 AS compute_col
FROM mytable
WHERE col5 = val;
```

使用视图时基本和普通的表没有差别，除了在进行增删改数据时存在某些限制。具体情况：
1. **视图与表是一对一关系情况**：如果没有其它约束（**如视图中没有的字段，在基本表中是必填字段情况**），是可以进行**增删改**数据操作
2. **视图与表是一对多关系情况**：如果只修改一张表的数据，且没有其它约束（**如视图中没有的字段，在基本表中是必填字段情况**），是可以进行**改**数据操作。

## 存储过程
SQL语句需要先编译然后执行，而**存储过程（Stored Procedure）是一组为了完成特定功能的SQL语句集，经编译后存储在数据库中，用户通过指定存储过程的名字并给定参数（如果该存储过程带有参数）来调用执行它**。

数据库中的存储过程可以看做是对编程中面向对象方法的模拟，它允许控制数据的访问方式。

使用存储过程的好处：

- 代码封装，保证了一定的安全性；
- 代码复用；
- 由于是预先编译，因此具有很高的性能。

命令行中创建存储过程需要自定义分隔符，因为命令行是以 ; 为结束符，而存储过程中也包含了分号，因此会错误把这部分分号当成是结束符，造成语法错误。
包含 in、out 和 inout 三种参数。

给变量赋值都需要用 select into 语句。

每次只能给一个变量赋值，不支持集合的操作。
```sql
delimiter //
CREATE PROCEDURE myprocedure (OUT ret INT)
BEGIN

DECLARE y INT ; 
SELECT sum(col1)
FROM mytable
INTO y ; 
SELECT y * y INTO ret ;
	
END//

delimiter;
```
```sql
call myprocedure(@ret);
select @ret;
```
[存储过程详解](https://www.cnblogs.com/mark-chan/p/5384139.html)


## 事务
事务处理是一种机制，通过确保成批的 SQL 操作要么完全执行，要么完全不执行，来维护数据库的完整性，保证数据库不包含不完整的操作结果
- **事务**（transaction）指一组 SQL 语句；
- **回退**（rollback）指撤销指定 SQL 语句的过程；
- **提交**（commit）指将未存储的 SQL 语句结果写入数据库表；
- **保留点**（savepoint）指事务处理中设置的临时占位符（placeholder），你可以对它发布回退（与回退整个事务处理不同）。

不能回退 SELECT 语句，回退 SELECT 语句也没意义；也不能回退 CREATE 和 DROP 语句。

MySQL 的事务提交默认是隐式提交，每执行一条语句就把这条语句当成一个事务然后进行提交。当出现 START TRANSACTION 语句时，会关闭隐式提交；当 COMMIT 或 ROLLBACK 语句执行后，事务会自动关闭，重新恢复隐式提交。

通过设置 autocommit 为 0 可以取消自动提交`SET autocommit = 0;`；autocommit 标记是针对每个连接而不是针对服务器的。

如果没有设置保留点，ROLLBACK 会回退到 START TRANSACTION 语句处；如果设置了保留点，并且在 ROLLBACK 中指定该保留点，则会回退到该保留点。
```sql
START TRANSACTION
// ...
SAVEPOINT delete1
// ...
ROLLBACK TO delete1
// ...
COMMIT
```
## 游标
在存储过程中使用游标可以对一个结果集进行移动遍历。

游标主要用于交互式应用，其中用户需要对数据集中的任意行进行浏览和修改。

游标对基于Web的应用用处不大。虽然游标在客户端应用和服务器会话期间存在，但这种客户/服务器模式不适合 Web应用，因为应用服务器是数据库客户端而不是最终用户。

使用游标的四个步骤：
 
1. 声明游标，这个过程没有实际检索出数据；
2. 打开游标；
3. 取出数据；
4. 关闭游标；
```sql
delimiter //
CREATE PROCEDURE myprocedure (OUT ret INT)
BEGIN

DECLARE done boolean DEFAULT 0 ;
DECLARE mycursor CURSOR FOR SELECT
	col1
FROM
	mytable ; # 定义了一个 continue handler，当 sqlstate '02000' 这个条件出现时，会执行 set done = 1
DECLARE CONTINUE HANDLER FOR SQLSTATE '02000'
SET done = 1 ; OPEN mycursor ;
REPEAT
	FETCH mycursor INTO ret ; SELECT
		ret ; UNTIL done
	END
	REPEAT
		; CLOSE mycursor ;
	END//

delimiter ;
```
## 索引
索引用来排序数据以加快搜索和排序操作的速度。可以在一个或多个列上定义索引，使 DBMS保存其内容的一个排过序的列表。在定义了索引后，DBMS 以使用书的索引类似的方法使用它

- 索引改善检索操作的性能，但降低了数据插入、修改和删除的性能。在执行这些操作时，DBMS必须动态地更新索引。
- 索引数据可能要占用大量的存储空间
- 可以在索引中定义多个列（例如，州加上城市）。这样的索引仅在以州加城市的顺序排序时有用。如果想按城市排序，则这种索引没有用处。
```sql
CREATE INDEX prod_name_ind
ON Products (prod_name);
```
## 触发器
触发器会在某个表执行以下语句时而自动执行：DELETE、INSERT、UPDATE。

触发器必须指定在语句执行之前还是之后自动执行，之前执行使用 BEFORE 关键字，之后执行使用 AFTER 关键字。BEFORE 用于数据验证和净化，AFTER 用于审计跟踪，将修改记录到另外一张表中。

INSERT 触发器包含一个名为 NEW 的虚拟表。
```sql
CREATE TRIGGER triggerName
AFTER / BEFORE INSERT / UPDATE / DELETE ON 表名
FOR EACH ROW #这句话在mysql是固定的
BEGIN
	sql语句;

END;
```
[触发器的详细理解](https://blog.csdn.net/eastmount/article/details/52344036#)
## 用户管理
MySQL 的账户信息保存在 mysql 这个数据库中。

```sql
USE mysql;
SELECT user FROM user;
```

**创建账户** 

新创建的账户没有任何权限。

```sql
CREATE USER myuser IDENTIFIED BY 'mypassword';
```

**修改账户名** 

```sql
RENAME myuser TO newuser;
```

**删除账户** 

```sql
RENAME USER myuser TO newuser;
```

**查看权限** 

```sql
SHOW GRANTS FOR myuser;
```

**授予权限** 

账户用 username@host 的形式定义，username@% 使用的是默认主机名。

```sql
GRANT SELECT, INSERT ON mydatabase.* TO myuser;
```

**删除权限** 

GRANT 和 REVOKE 可在几个层次上控制访问权限：

- 整个服务器，使用 GRANT ALL 和 REVOKE ALL；
- 整个数据库，使用 ON database.\*；
- 特定的表，使用 ON database.table；
- 特定的列；
- 特定的存储过程。

```sql
REVOKE SELECT, INSERT ON mydatabase.* FROM myuser;
```

**更改密码** 

必须使用 Password() 函数

```sql
SET PASSWROD FOR myuser = Password('new_password');
```