# SQL-Coding

# 184. Largest Salary by Department

Given a table called `employees`, with the following schema:

| Column     | Type     |
|------------|----------|
| id         | INTEGER  |
| department | VARCHAR  |
| salary     | INTEGER  |

The goal is to get the largest salary of any employee by department.

The desired output schema is:

| Column         | Type     |
|----------------|----------|
| department     | VARCHAR  |
| largest_salary | INTEGER  |

## Solution

```sql
SELECT department, MAX(salary) AS largest_salary
FROM employees
GROUP BY department;
```

# 1757. Recyclable and Low Fat Products

Given a table `products` with the following schema:

| Column     | Type                                                        |
|------------|-------------------------------------------------------------|
| product_id | INTEGER                                                     |
| low_fats   | ENUM |
| recyclable | ENUM  |

- `product_id` is the primary key for this table.
- ('Y', 'N') - 'Y' means the product is low fat, 'N' means it's not
- ('Y', 'N') - 'Y' means the product is recyclable, 'N' means it's not

## Problem

Write a solution to find the ids of products that are both low fat and recyclable. Return the result table in any order.

### Example

**Input**:

| product_id | low_fats | recyclable |
|------------|----------|------------|
| 0          | Y        | N          |
| 1          | Y        | Y          |
| 2          | N        | Y          |
| 3          | Y        | Y          |
| 4          | N        | N          |

**Output**:

| product_id |
|------------|
| 1          |
| 3          |

## Solution

```sql
SELECT product_id
FROM products
WHERE low_fats = 'Y' AND recyclable = 'Y';
```
# 584. Find Customer Referee

Given a `customer` table with the following schema:

| Column     | Type     |
|------------|----------|
| id         | INTEGER  |
| name       | VARCHAR  |
| referee_id | INTEGER  |

- `id` is the primary key for this table.
- Each row indicates the id of a customer, their name, and the id of the customer who referred them.

## Problem

Find the names of the customers who are **not** referred by the customer with `id = 2`. Return the result table in any order.

### Example

**Input**:

| id | name | referee_id |
|----|------|------------|
| 1  | Will | NULL       |
| 2  | Jane | NULL       |
| 3  | Alex | 2          |
| 4  | Bill | NULL       |
| 5  | Zack | 1          |
| 6  | Mark | 2          |

**Output**:

| name |
|------|
| Will |
| Jane |
| Bill |
| Zack |

## Solutions

### Solution 1
```sql
SELECT name
FROM customer
WHERE referee_id != '2' OR referee_id IS NULL;
```
### Solution 2
```sql
SELECT name
FROM customer
WHERE COALESCE(referee_id,'') != 2  -- COALESCE function is used to handle NULL values. If referee_id is NULL, it is replaced with an empty string --
```
### Solution 3
```sql
SELECT name
FROM customer
WHERE isnull(referee_id) OR referee_id != '2' 
```
# 180. Consecutive Numbers

Given a table called `Logs`, with the following schema:

| Column     | Type     |
|------------|----------|
| id         | INTEGER  |
| num | VARCHAR  |

- In SQL, id is the primary key for this table.
- `id` is an autoincrement column.

Find all numbers that appear at least three times consecutively.

Return the result table in *any order*.

The result format is in the following example.

### Example
**Input**:

| id         | num     |
|------------|----------|
| 1     | 1  |
| 2 | 1  |
| 3     | 1  |
| 4 | 2  |
| 5     | 1  |
| 6 | 2  |
| 7 | 2  |

**Output**:

| ConsecutiveNums |
|------|
| 1 |


### Solution 1

```sql
SELECT DISTINCT log1.num AS ConsecutiveNums
FROM
    Logs  log1, -- temporary tables
    Logs  log2,
    Logs  log3
WHERE log1.id = log2.id -1 AND -- check that these are in consecutive order
      log2.id = log3.id -1 AND
      log1.num = log2.num AND -- check that the values are the same
      log2.num = log3.num 
```
### Solution 2 
#### Using Lead Function
``` sql 
WITH cte as(
SELECT id,num
WHERE lead(num,1) OVER () num1,
      lead(num,2) OVER () num2
FROM logs
)

SELECT DISTINCT(num) ConsecutiveNums 
FROM cte WHERE (num=num1) AND (num=num2)
```

# 1164. Product Price at a Given Date

Given a table called `Products` with columns `product_id`, `new_price`, and `change_date`, where `(product_id, change_date)` forms the primary key, find the prices of all products on `2019-08-16`. Assume the price of all products before any change is `10`.

#### Example

**Input:**

| product_id | new_price | change_date |
|------------|-----------|-------------|
| 1          | 20        | 2019-08-14  |
| 2          | 50        | 2019-08-14  |
| 1          | 30        | 2019-08-15  |
| 1          | 35        | 2019-08-16  |
| 2          | 65        | 2019-08-17  |
| 3          | 20        | 2019-08-18  |

**Output:**

| product_id | price |
|------------|-------|
| 2          | 50    |
| 1          | 35    |
| 3          | 10    |

### Solution

```sql
SELECT product_id, new_price AS price
FROM products
WHERE (product_id, change_date) IN
(
    SELECT product_id, max(change_date)
    FROM products 
    WHERE change_date <= "2019-08-16"
    GROUP BY product_id
)
UNION
SELECT product_id, 10 AS price
FROM products
WHERE (product_id) NOT IN
(
    SELECT product_id
    FROM products 
    WHERE change_date <= "2019-08-16"
)
```
# 1978. Employees Whose Manager Left the Company

Given a table called `Employees` with columns `employee_id`, `name`, `manager_id`, and `salary`, find the IDs of employees whose salary is less than $30000 and whose manager has left the company. When a manager leaves, their information is deleted from the `Employees` table, but their former reports still have their `manager_id` set.

#### Example

**Input:**

| employee_id | name      | manager_id | salary |
|-------------|-----------|------------|--------|
| 3           | Mila      | 9          | 60301  |
| 12          | Antonella | null       | 31000  |
| 13          | Emery     | null       | 67084  |
| 1           | Kalel     | 11         | 21241  |
| 9           | Mikaela   | null       | 50937  |
| 11          | Joziah    | 6          | 28485  |

**Output:**

| employee_id |
|-------------|
| 11          |

### Solution

```sql
SELECT employee_id
FROM employees
WHERE salary < 30000
  AND manager_id NOT IN (
      SELECT employee_id
      FROM employees
  )
ORDER BY employee_id;
```
# 1667. Fixing User Names

Given a table called `Users` with columns `user_id` and `name`, where `user_id` is the primary key, correct the formatting of names so that only the first character is uppercase and the rest are lowercase.

## Example

**Input:**

Users table:

| user_id | name  |
|---------|-------|
| 1       | aLice |
| 2       | bOB   |

**Output:**

| user_id | name  |
|---------|-------|
| 1       | Alice |
| 2       | Bob   |

## Solution

```sql
UPDATE Users
SET name = CONCAT(UPPER(SUBSTRING(name, 1, 1)), LOWER(SUBSTRING(name, 2)))
ORDER BY user_id;
```
# 1204. Last Person to Fit in the Bus

There is a queue of people waiting to board a bus. Each person has a `person_id`, `person_name`, `weight`, and `turn` indicating the order of boarding. The bus has a weight limit of 1000 kilograms. Write a SQL query to find the `person_name` of the last person that can fit on the bus without exceeding the weight limit.

## Example

**Input:**

Queue table:

| person_id | person_name | weight | turn |
|-----------|-------------|--------|------|
| 5         | Alice       | 250    | 1    |
| 4         | Bob         | 175    | 5    |
| 3         | Alex        | 350    | 2    |
| 6         | John Cena   | 400    | 3    |
| 1         | Winston     | 500    | 6    |
| 2         | Marie       | 200    | 4    |

**Output:**

| person_name |
|-------------|
| John Cena   |

**Explanation:**
The table is ordered by `turn` for simplicity.

| Turn | ID | Name      | Weight | Total Weight |
|------|----|-----------|--------|--------------|
| 1    | 5  | Alice     | 250    | 250          |
| 2    | 3  | Alex      | 350    | 600          |
| 3    | 6  | John Cena | 400    | 1000         | (last person to board)
| 4    | 2  | Marie     | 200    | 1200         | (cannot board)
| 5    | 4  | Bob       | 175    | ___          |
| 6    | 1  | Winston   | 500    | ___          |

## Solution

```sql
SELECT person_name
FROM (
    SELECT person_name, SUM(weight) OVER (ORDER BY turn) AS total_weight
    FROM Queue
) AS cumulative
WHERE total_weight <= 1000
ORDER BY turn DESC
LIMIT 1;
```

# 1907. Count Salary Categories

## Problem Description

You are given a table `Accounts` with the following schema:

| Column Name | Type |
|-------------|------|
| account_id  | int  |
| income      | int  |

`account_id` is the primary key (column with unique values) for this table. Each row contains information about the monthly income for one bank account.

Write a SQL query to calculate the number of bank accounts for each salary category. The salary categories are:

- "Low Salary": All salaries strictly less than $20000.
- "Average Salary": All salaries in the inclusive range [$20000, $50000].
- "High Salary": All salaries strictly greater than $50000.

The result table must contain all three categories. If there are no accounts in a category, return 0.

### Example

#### Input

Accounts table:

| account_id | income |
|------------|--------|
| 3          | 108939 |
| 2          | 12747  |
| 8          | 87709  |
| 6          | 91796  |

#### Output

| category       | accounts_count |
|----------------|----------------|
| Low Salary     | 1              |
| Average Salary | 0              |
| High Salary    | 3              |

#### Explanation

- **Low Salary**: Account 2.
- **Average Salary**: No accounts.
- **High Salary**: Accounts 3, 6, and 8.

The result table should be returned in any order.

### Solution

```sql
SELECT 'Low Salary' AS category, SUM(CASE WHEN income < 20000 then 1 ELSE 0 END) AS accounts_count
FROM Accounts

UNION

SELECT 'Average Salary' AS category, SUM(CASE WHEN income BETWEEN 20000 AND 50000 then 1 ELSE 0 END) AS accounts_count
FROM Accounts

UNION

SELECT 'High Salary' AS category, SUM(CASE WHEN income > 50000 then 1 ELSE 0 END) AS accounts_count
FROM Accounts
```

# 602. Friend Requests II: Who Has the Most Friends

### Table: RequestAccepted

| Column Name  | Type    |
|--------------|---------|
| requester_id | int     |
| accepter_id  | int     |
| accept_date  | date    |

- `(requester_id, accepter_id)` is the primary key (combination of columns with unique values) for this table.
- This table contains the ID of the user who sent the request, the ID of the user who received the request, and the date when the request was accepted.

### Task

Write a solution to find the people who have the most friends and the total number of friends.

The test cases are generated so that only **one person** has the most friends.

### Example

**Input:**

RequestAccepted table:

| requester_id | accepter_id | accept_date |
|--------------|-------------|-------------|
| 1            | 2           | 2016/06/03  |
| 1            | 3           | 2016/06/08  |
| 2            | 3           | 2016/06/08  |
| 3            | 4           | 2016/06/09  |

**Output:**

| id | num |
|----|-----|
| 3  | 3   |

**Explanation:**

The person with `id = 3` is a friend of people 1, 2, and 4, so they have three friends in total, which is more than anyone else.

### Solution

```sql
   WITH cte as ( 
    SELECT
        id,
        num, 
        dense_rank() over(order by num DESC) as rnk
    FROM (
    SELECT id, count(id) num
    FROM
        (
        SELECT requester_id AS id, accepter_id
        FROM RequestAccepted
        UNION
        SELECT accepter_id AS id, requester_id
        FROM RequestAccepted) id
    GROUP BY id
    ORDER BY num DESC
    ) rnk_table
   ) 
   SELECT id, num
   FROM cte
   WHERE rnk = 1
```

# 595. Big Countries

### Table: `World`

| Column Name | Type    |
|-------------|---------|
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | bigint  |

`name` is the primary key (column with unique values) for this table.
Each row of this table gives information about the name of a country, the continent to which it belongs, its area, the population, and its GDP value.

---

A country is **big** if:
- it has an area of at least three million (i.e., `3000000 km2`), or
- it has a population of at least twenty-five million (i.e., `25000000`).

Write a solution to find the name, population, and area of the big countries.

Return the result table in any order. The result format is in the following example.

---

### Example 1

**Input:**

`World` table:

| name        | continent | area    | population | gdp          |
|-------------|-----------|---------|------------|--------------|
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |

**Output:**

| name        | population | area    |
|-------------|------------|---------|
| Afghanistan | 25500100   | 652230  |
| Algeria     | 37100000   | 2381741 |


### Solution

```sql
SELECT 
name, 
population, 
area
FROM world
WHERE area >= 3000000 OR population >= 25000000
```

# 1148. Article Views I

### Table: `Views`

| Column Name | Type |
|-------------|------|
| article_id  | int  |
| author_id   | int  |
| viewer_id   | int  |
| view_date   | date |

There is no primary key (column with unique values) for this table, the table may have duplicate rows.
Each row of this table indicates that some viewer viewed an article (written by some author) on some date.
Note that equal `author_id` and `viewer_id` indicate the same person.

---

Write a solution to find all the authors that viewed at least one of their own articles.

Return the result table sorted by `id` in ascending order. The result format is in the following example.

---

### Example 1

**Input:**

`Views` table:

| article_id | author_id | viewer_id | view_date  |
|------------|-----------|-----------|------------|
| 1          | 3         | 5         | 2019-08-01 |
| 1          | 3         | 6         | 2019-08-02 |
| 2          | 7         | 7         | 2019-08-01 |
| 2          | 7         | 6         | 2019-08-02 |
| 4          | 7         | 1         | 2019-07-22 |
| 3          | 4         | 4         | 2019-07-21 |
| 3          | 4         | 4         | 2019-07-21 |

**Output:**

| id |
|----|
| 4  |
| 7  |

### Solution

```sql
SELECT author_id as id
FROM views
WHERE author_id = viewer_id
GROUP BY author_id
ORDER BY author_id ASC

```

# 1683. Invalid Tweets
## Table: `Tweets`

| Column Name | Type    |
|-------------|---------|
| tweet_id    | int     |
| content     | varchar |

`tweet_id` is the primary key (column with unique values) for this table.
`content` consists of alphanumeric characters, `!`, or ` ` and no other special characters.
This table contains all the tweets in a social media app.

---

Write a solution to find the IDs of the invalid tweets. The tweet is invalid if the number of characters used in the content of the tweet is strictly greater than `15`.

Return the result table in any order. The result format is in the following example.

---

### Example 1

**Input:**

`Tweets` table:

| tweet_id | content                           |
|----------|-----------------------------------|
| 1        | Let us Code                       |
| 2        | More than fifteen chars are here! |

**Output:**

| tweet_id |
|----------|
| 2        |

**Explanation:**
Tweet 1 has length = 11. It is a valid tweet.
Tweet 2 has length = 33. It is an invalid tweet.

### Solution

```sql
select tweet_id
from tweets
where CHAR_LENGTH(content) > 15 
```

# 1068. Product Sales Analysis I 
## Table: `Sales`

| Column Name | Type |
|-------------|------|
| sale_id     | int  |
| product_id  | int  |
| year        | int  |
| quantity    | int  |
| price       | int  |

`(sale_id, year)` is the primary key (combination of columns with unique values) of this table.
`product_id` is a foreign key (reference column) to the `Product` table.
Each row of this table shows a sale on the product `product_id` in a certain year.
Note that the price is per unit.

---

## Table: `Product`

| Column Name  | Type    |
|--------------|---------|
| product_id   | int     |
| product_name | varchar |

`product_id` is the primary key (column with unique values) of this table.
Each row of this table indicates the product name of each product.

---

Write a solution to report the `product_name`, `year`, and `price` for each `sale_id` in the `Sales` table.

Return the resulting table in any order. The result format is in the following example.

---

### Example 1

**Input:**

`Sales` table:

| sale_id | product_id | year | quantity | price |
|---------|------------|------|----------|-------|
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |

`Product` table:

| product_id | product_name |
|------------|--------------|
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |

**Output:**

| product_name | year | price |
|--------------|------|-------|
| Nokia        | 2008 | 5000  |
| Nokia        | 2009 | 5000  |
| Apple        | 2011 | 9000  |

**Explanation:**
From sale_id = 1, we can conclude that Nokia was sold for 5000 in the year 2008.
From sale_id = 2, we can conclude that Nokia was sold for 5000 in the year 2009.
From sale_id = 7, we can conclude that Apple was sold for 9000 in the year 2011.

### Solution
```sql
select product.product_name, sales.year, sales.price
from sales
join product
on sales.product_id = product.product_id
```


# 197. Rising Temperature
## Table: `Weather`

| Column Name | Type |
|-------------|------|
| id          | int  |
| recordDate  | date |
| temperature | int  |

`id` is the column with unique values for this table.
There are no different rows with the same `recordDate`.
This table contains information about the temperature on a certain day.

---

Write a solution to find all dates' `id` with higher temperatures compared to its previous dates (yesterday).

Return the result table in any order. The result format is in the following example.

---

### Example 1

**Input:**

`Weather` table:

| id | recordDate | temperature |
|----|------------|-------------|
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |

**Output:**

| id |
|----|
| 2  |
| 4  |

**Explanation:**
In 2015-01-02, the temperature was higher than the previous day (10 -> 25).
In 2015-01-04, the temperature was higher than the previous day (20 -> 30).

### Solution
```sql
SELECT w1.id
FROM Weather w1, Weather w2
WHERE DATEDIFF(w1.recordDate, w2.recordDate) = 1 AND w1.temperature > w2.temperature; #datediff to make sure that they are consecutive days (with a difference of 1 day)
```

# 577. Employee Bonus
## Table: `Employee`

| Column Name | Type    |
|-------------|---------|
| empId       | int     |
| name        | varchar |
| supervisor  | int     |
| salary      | int     |

`empId` is the column with unique values for this table.
Each row of this table indicates the name and the ID of an employee in addition to their salary and the id of their manager.

---

## Table: `Bonus`

| Column Name | Type |
|-------------|------|
| empId       | int  |
| bonus       | int  |

`empId` is the column of unique values for this table.
`empId` is a foreign key (reference column) to `empId` from the `Employee` table.
Each row of this table contains the id of an employee and their respective bonus.

---

Write a solution to report the name and bonus amount of each employee who satisfies either of the following:
- The employee has a bonus less than `1000`.
- The employee did not get any bonus.

Return the result table in any order. The result format is in the following example.

---

### Example 1

**Input:**

`Employee` table:

| empId | name   | supervisor | salary |
|-------|--------|------------|--------|
| 3     | Brad   | null       | 4000   |
| 1     | John   | 3          | 1000   |
| 2     | Dan    | 3          | 2000   |
| 4     | Thomas | 3          | 4000   |

`Bonus` table:

| empId | bonus |
|-------|-------|
| 2     | 500   |
| 4     | 2000  |

**Output:**

| name | bonus |
|------|-------|
| Brad | null  |
| John | null  |
| Dan  | 500   |

### Solution
```sql
select e.name, b.bonus
from employee e left join bonus b on
e.empId = b.empId
where bonus < 1000 or bonus IS NULL
```


# 185. Department Top Three Salaries
## Table: `Employee`

| Column Name  | Type    |
|--------------|---------|
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |

`id` is the primary key (column with unique values) for this table.
`departmentId` is a foreign key (reference column) of the ID from the `Department` table.
Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.

---

## Table: `Department`

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

`id` is the primary key (column with unique values) for this table.
Each row of this table indicates the ID of a department and its name.

---

A company's executives are interested in seeing who earns the most money in each of the company's departments. A high earner in a department is an employee who has a salary in the top three unique salaries for that department.

Write a solution to find the employees who are high earners in each of the departments.

Return the result table in any order. The result format is in the following example.

---

### Example 1

**Input:**

`Employee` table:

| id | name  | salary | departmentId |
|----|-------|--------|--------------|
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |

`Department` table:

| id | name  |
|----|-------|
| 1  | IT    |
| 2  | Sales |

**Output:**

| Department | Employee | Salary |
|------------|----------|--------|
| IT         | Max      | 90000  |
| IT         | Joe      | 85000  |
| IT         | Randy    | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |

**Explanation:**

In the IT department:
- Max earns the highest unique salary
- Both Randy and Joe earn the second-highest unique salary
- Will earns the third-highest unique salary

In the Sales department:
- Henry earns the highest salary
- Sam earns the second-highest salary
- There is no third-highest salary as there are only two employees

---

**Constraints:**
- There are no employees with the exact same name, salary, and department.

### Solution
```sql
SELECT name AS Employee, salary AS Salary, Department
FROM (
    SELECT e.name, e.salary,
           d.name AS Department,
           DENSE_RANK() OVER (PARTITION BY d.id ORDER BY e.salary DESC) AS rnk
    FROM employee e JOIN department d ON e.departmentId = d.id
) AS ranked
WHERE rnk <= 3
ORDER BY Department, salary DESC;
```


# 176. Second Highest Salary
## Table: `Employee`

| Column Name | Type |
|-------------|------|
| id          | int  |
| salary      | int  |

`id` is the primary key (column with unique values) for this table.
Each row of this table contains information about the salary of an employee.

---

Write a solution to find the second highest distinct salary from the `Employee` table. If there is no second highest salary, return `null` (return `None` in Pandas).

The result format is in the following example.

---

### Example 1

**Input:**

`Employee` table:

| id | salary |
|----|--------|
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |

**Output:**

| SecondHighestSalary |
|---------------------|
| 200                 |

---

### Example 2

**Input:**

`Employee` table:

| id | salary |
|----|--------|
| 1  | 100    |

**Output:**

| SecondHighestSalary |
|---------------------|
| null                |

### Solution
```sql
SELECT(
SELECT salary
FROM (
    SELECT salary,id,
    DENSE_RANK() OVER (ORDER BY salary DESC) as rnk
    FROM employee
    ) as ranked
WHERE rnk = 2 
LIMIT 1 #only get 1 value if there is a tie
)  AS SecondHighestSalary #to get a null value
```
