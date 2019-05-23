
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
# Write your MySQL query statement below
/*一开始是这样写的，问题：
使用了GROUP BY进行分组后,SELECT语句中只允许出现分组字段和多行函数
SELECT d.Name AS Department,e.Name AS Employee,MAX(e.Salary)
FROM Employee AS e
INNER JOIN Department AS d
ON e.DepartmentId = d.Id
GROUP BY e.DepartmentId;
*/

-- 创建一个只包含DepartmentId和MAX(Salary)的临时表进行分组
SELECT d.Name AS Department,e.Name AS Employee,t.Salary
FROM Employee AS e
INNER JOIN Department AS d
ON e.DepartmentId = d.Id
INNER JOIN (SELECT DepartmentId,MAX(Salary) AS Salary
            FROM Employee
            GROUP BY DepartmentId) AS t
ON e.DepartmentId = t.DepartmentId AND e.Salary = t.Salary;
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
## 569. Median Employee Salary

## 570. Managers with at Least 5 Direct Reports
```sql

```


# 牛客网
- [牛客网 SQL练习](https://www.nowcoder.com/ta/sql)

## 1. 查找最晚入职员工的所有信息
```sql
SELECT *
FROM employees
ORDER BY hire_date DESC
LIMIT 0,1;
```

## 2.查找入职员工时间排名倒数第三的员工所有信息
```sql
SELECT *
FROM employees
ORDER BY hire_date DESC
LIMIT 2,1;
```

## 3. 查找各个部门当前(to_date='9999-01-01')领导当前薪水详情以及其对应部门编号dept_no
```sql
SELECT s.*,d.dept_no
FROM salaries AS s
INNER JOIN dept_manager AS d
ON d.emp_no = s.emp_no
WHERE d.to_date = '9999-01-01' AND s.to_date = '9999-01-01';
```

## 4. 查找所有已经分配部门的员工的last_name和first_name
```sql
SELECT e.last_name,e.first_name,d.dept_no
FROM employees AS e
INNER JOIN dept_emp AS d
ON e.emp_no = d.emp_no;
```

## 5. 查找所有员工的last_name和first_name以及对应部门编号dept_no，也包括展示没有分配具体部门的员工
```sql
SELECT e.last_name,e.first_name,d.dept_no
FROM employees AS e
LEFT JOIN dept_emp AS d
ON e.emp_no = d.emp_no;
```

## 6.查找所有员工入职时候的薪水情况，给出emp_no以及salary， 并按照emp_no进行逆序
```sql
SELECT e.emp_no,s.salary
FROM employees AS e
INNER JOIN salaries AS s
ON e.emp_no = s.emp_no AND e.hire_date = s.from_date
ORDER BY e.emp_no DESC;
```

## 7.  查找薪水涨幅超过15次的员工号emp_no以及其对应的涨幅次数t  
本体题目出的有问题，语义不清
```sql
SELECT emp_no, COUNT(emp_no) AS t
FROM salaries 
GROUP BY emp_no
HAVING t > 15
```

## 8.找出所有员工当前(to_date='9999-01-01')具体的薪水salary情况，对于相同的薪水只显示一次,并按照逆序显示
```sql
SELECT DISTINCT salary
FROM salaries
WHERE to_date = '9999-01-01'
ORDER BY salary DESC;
```
## 9.获取所有部门当前manager的当前薪水情况，给出dept_no, emp_no以及salary，当前表示to_date='9999-01-01'
```sql
SELECT d.dept_no,d.emp_no,s.salary
FROM dept_manager AS d
INNER JOIN salaries AS s
ON d.emp_no = s.emp_no
WHERE d.to_date = '9999-01-01' AND s.to_date = '9999-01-01';
```

## 10. 获取所有非manager的员工emp_no
```sql
SELECT emp_no
FROM employees
WHERE emp_no NOT IN (SELECT emp_no
                    FROM dept_manager);
```
## 11.获取所有员工当前的manager，如果当前的manager是自己的话结果不显示，当前表示to_date='9999-01-01'。
```sql
SELECT de.emp_no,dm.emp_no AS manager_no
FROM dept_emp AS de
INNER JOIN dept_manager AS dm
ON de.emp_no <> dm.emp_no AND de.dept_no = dm.dept_no
WHERE de.to_date = '9999-01-01' AND dm.to_date = '9999-01-01';
```

## 12.获取所有部门中当前员工薪水最高的相关信息，给出dept_no, emp_no以及其对应的salary
```sql
SELECT de.dept_no,de.emp_no,MAX(s.salary)
FROM dept_emp AS de
INNER JOIN salaries AS s
ON de.emp_no = s.emp_no
WHERE de.to_date = '9999-01-01' AND s.to_date = '9999-01-01'
GROUP BY de.dept_no;
```
## 13. 从titles表获取按照title进行分组，每组个数大于等于2，给出title以及对应的数目t。
```sql
SELECT title,COUNT(title) AS t
FROM titles
GROUP BY title
HAVING t >= 2;
```

## 14. 从titles表获取按照title进行分组，每组个数大于等于2，给出title以及对应的数目t。注意对于重复的emp_no进行忽略。
```sql
SELECT title,COUNT(DISTINCT emp_no) AS t
FROM titles
GROUP BY title
HAVING t >= 2;
```

## 15. 查找employees表所有emp_no为奇数，且last_name不为Mary的员工信息，并按照hire_date逆序排列
```sql
SELECT *
FROM employees
WHERE emp_no%2 <> 0 AND last_name <> 'Mary'
ORDER BY hire_date DESC;
```

## 16.统计出当前各个title类型对应的员工当前薪水对应的平均工资。结果给出title以及平均工资avg。
```sql
SELECT t.title,AVG(s.salary) AS avg
FROM titles AS t
INNER JOIN salaries AS s
ON t.emp_no = s.emp_no
WHERE s.to_date = '9999-01-01' AND t.to_date = '9999-01-01'
GROUP BY t.title;
```

## 17. 获取当前（to_date='9999-01-01'）薪水第二多的员工的emp_no以及其对应的薪水salary
```sql
SELECT emp_no,salary
FROM salaries
WHERE to_date = '9999-01-01'
ORDER BY salary DESC
LIMIT 1,1
```

## 18. 查找当前薪水(to_date='9999-01-01')排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，不准使用order by
```sql
SELECT e.emp_no,MAX(s.salary) AS salary,e.last_name,e.first_name
FROM employees AS e
INNER JOIN salaries AS s
ON e.emp_no = s.emp_no
WHERE s.to_date = '9999-01-01'
AND s.salary not in (SELECT MAX(salary) FROM salaries WHERE to_date = '9999-01-01');
```

## 19. 查找所有员工的last_name和first_name以及对应的dept_name，也包括暂时没有分配部门的员工
```sql
SELECT e.last_name,e.first_name,d.dept_name
FROM employees AS e
LEFT OUTER JOIN dept_emp AS de
ON e.emp_no = de.emp_no
LEFT JOIN departments AS d
ON de.dept_no = d.dept_no;
```

## 20. 查找员工编号emp_no为10001其自入职以来的薪水salary涨幅值growth
```sql
SELECT ( 
(SELECT salary FROM salaries WHERE emp_no = 10001 ORDER BY to_date DESC LIMIT 1) -
(SELECT salary FROM salaries WHERE emp_no = 10001 ORDER BY from_date ASC LIMIT 1)
) AS growth;
```

##
```sql
```

##
```sql
```