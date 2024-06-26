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
