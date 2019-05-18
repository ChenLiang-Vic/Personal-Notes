
# SQL学习
## 基础
### 注意事项
1. 模式定义了数据如何存储、存储什么样的数据以及数据如何分解等信息，数据库和表都有模式。

2. 主键的值不允许修改，也不允许复用（不能使用已经删除的主键值赋给新数据行的主键）。

3. SQL 语句不区分大小写，但是数据库表名、列名和值是否区分依赖于具体的 DBMS 以及配置。
4. 对于;单行SQL语句可以不写,多行SQL语句必须要写,所以不管单行多行写上;即可
### 注释
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

### 删除数据DELETE

### 修改数据UPDATE

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
##### 单条件筛选
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
##### 组合条件筛选
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
##### 通配符筛选
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
##### 汇总函数 
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
##### 文本处理函数
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
##### 日期和时间处理函数
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
SELECT NOW();
```
##### 数值处理函数
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
##### 分组查询GROUP BY
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
##### 分组筛选HAVING
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
##### 查询结果只有一个数据
```sql
#查询比平均价格高的商品信息
SELECT *
FROM products
WHERE prod_price > (SELECT AVG(DISTINCT prod_price) FROM products);
```
##### 查询结果为一个字段的数据
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
1. **联结用于联结多个表，使用JOIN关键字，并且条件语句使用ON而不是WHERE。**

2. **联结可以替换子查询，并且比子查询的效率一般会更快。**

3. **可以用AS给列名、计算字段和表名取别名，给表名取别名是为了简化SQL语句以及连接相同表。**

### 自然联结
### 外联结
#### 左外联结
#### 右外联结
#### 全外联结
### 自联结

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
## 二维表操作

### 二维表创建
### 二维表维护

## 用户管理