
# leetcode

[leetcode SQL练习](https://leetcode.com/problemset/database/)

## 175.Combine Two Tables

```sql
SELECT p.FirstName,p.LastName,a.City,a.State
FROM Person AS p
LEFT OUTER JOIN Address AS a
ON p.PersonId = a.PersonId;
```

## 176. Second Highest Salary
```sql
/*使用LIMIT无法保证没有第二高时返回NULL
SELECT DISTINCT Salary AS SecondHighestSalary
FROM Employee
ORDER BY Salary DESC
LIMIT 1,1;
*/

-- 使用SELECT进行嵌套
SELECT (SELECT DISTINCT Salary
        FROM Employee
        ORDER BY Salary DESC
        LIMIT 1,1) AS SecondHighestSalary;
```

## 177. Nth Highest Salary
上面176题的扩展
```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  SET N = N-1;  -- 没有的话会报错
  RETURN (
      # Write your MySQL query statement below.
      SELECT(SELECT DISTINCT Salary
            FROM Employee
            ORDER BY Salary DESC
            LIMIT N,1)
  );
END
```
## 178. Rank Scores
```sql

```
## 180. Consecutive Numbers
```sql
-- 可以用表联结处理连续出现几次的问题
SELECT DISTINCT l1.Num AS ConsecutiveNums
FROM Logs AS l1
INNER JOIN Logs AS l2
ON l1.Num = l2.Num AND l1.Id = l2.Id - 1
INNER JOIN Logs AS l3
ON l2.Num = l3.Num AND l2.Id = l3.Id - 1; 
```
## 181. Employees Earning More Than Their Managers
```sql
SELECT e1.Name AS Employee
FROM Employee e1
INNER JOIN Employee e2
ON e1.ManagerId = e2.id AND e1.Salary > e2.Salary;
```
## 182. Duplicate Emails
```sql
-- 联结之后再筛选,筛选条件为不等
SELECT DISTINCT p1.Email
FROM Person p1
INNER JOIN Person p2
ON p1.Email = p2.Email AND p1.Id <> p2.Id;
```
## 183. Customers Who Never Order
```sql
-- 注意根据左外连接和右外连接的特点，是连接完成后才会出现NULL，所以不能使用ON进行筛选(和内联结不同，内联结是两个都可以)
SELECT c.Name AS Customers
FROM Customers AS c
LEFT JOIN Orders AS o 
ON c.Id = o.CustomerId
WHERE o.CustomerId is NULL;
```
## 184. Department Highest Salary
```sql
```
## 185. Department Top Three Salaries
```sql

```
## 196. Delete Duplicate Emails
```sql
-- 删除也可以使用联结进行筛选
DELETE p1
FROM Person p1
INNER JOIN Person p2
ON p1.Email = p2.Email AND p1.Id > p2.Id;
```
## 197. Rising Temperature
```sql
-- TO_DAYS函数 返回一个从年份0开始的天数
SELECT w2.Id
FROM Weather AS w1
INNER JOIN Weather AS w2
ON TO_DAYS(w2.RecordDate)-TO_DAYS(w1.RecordDate)=1 AND w2.Temperature > w1.Temperature;
```
## 262. Trips and Users
```sql

```
## 595. Big Countries
```sql
-- million是百万
SELECT name,population,area
FROM World
WHERE area > 3000000 OR population > 25000000;
```
## 596. Classes More Than 5 Students
```sql
-- 开始时没有考虑到学生去重，即使考虑到了也没有想到COUNT(DISTINCT student)
SELECT class
FROM courses
GROUP BY class
HAVING COUNT(DISTINCT student) >= 5;
```
##  601. Human Traffic of Stadium
```sql

```
## 620. Not Boring Movies
```sql
SELECT *
FROM cinema
WHERE (id%2 = 1) AND (description <> 'boring')
ORDER BY rating DESC;
```
## 626. Exchange Seats
```sql
-- 完整case函数
SELECT
CASE
WHEN id = (SELECT MAX(id) FROM seat) AND id %2=1 THEN id
WHEN id%2=0 THEN id -1
WHEN id%2 = 1 THEN id +1
END AS id, student
FROM seat
ORDER BY id;
```
## 627. Swap Salary
```sql
-- 简单case函数
UPDATE salary
SET sex = 
CASE
WHEN sex = 'm' THEN 'f'  
ELSE 'm'
END;
```
----
付费题目  大部分为Midium难度
## 570. Managers with at Least 5 Direct Reports
```sql

```


# 牛客网
- [牛客网 SQL练习](https://www.nowcoder.com/ta/sql)

## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```
## 
```sql
```