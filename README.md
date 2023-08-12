# Complete Sql 2023

# Advanced SQL Mastery :crystal_ball:

Welcome to my repository where I delve into advanced SQL topics. This project is a hub for discovering the intricacies of SQL through practical examples and straightforward explanations.

## What's Covered?

In this repository, you'll find basic yet insightful demonstrations of various advanced SQL concepts. From window functions to other advanced operations, we'll walk through fundamental techniques step by step.

Let's embark on this SQL journey together! :rocket:


# WINDOW FUNCTIONS ~
 
  # row_number()  :star:



```sql

-- Ever wondered how to conjure the first two employees from each department who embarked on the company adventure? Behold the `ROW_NUMBER()` spell:

SELECT *
FROM (
    SELECT e.*,
           ROW_NUMBER() OVER (PARTITION BY dept_name ORDER BY emp_id) AS rn
    FROM employees e
) AS x
WHERE x.rn < 3;

```

-------

  # Rank()  :star:


```sql

-- Ascend to greatness by summoning the top 3 earners in each department through the `RANK()` enchantment:

SELECT *
FROM (
    SELECT e.*,
           RANK() OVER (PARTITION BY dept_name ORDER BY salary DESC) AS rnk
    FROM employees e
) AS x
WHERE x.rnk < 4;

```

-------

  # Lead() & Lag()  :star:


```sql

-- Peer into the past and future with the mystic `LEAD()` and `LAG()` incantations, unlocking the wisdom of adjacent records:

SELECT e.*,
       LAG(salary) OVER (PARTITION BY dept_name ORDER BY emp_id) AS prev_emp_salary,
       LEAD(salary) OVER (PARTITION BY dept_name ORDER BY emp_id) AS next_emp_salary
FROM employees e;

```


  # Common Table Expressions ~ (With Clause or SubQuery Factoring)


```sql

-- select * from products;

-- Find the stores whose sales are greater than the average sales across all stores

-- METHOD 1 - SUB - QUERIES

-- 1.) Find the total sales per store


SELECT store_id, SUM(cost_in_cents) AS total_sales_per_store
FROM products
GROUP BY store_id;

-- 2.) Find the average sales across all stores


SELECT AVG(total_sales_per_store) AS avg_sales_for_all_stores
FROM (
    SELECT store_id, SUM(cost_in_cents) AS total_sales_per_store
    FROM products
    GROUP BY store_id
) AS x;

-- 3.) Find the stores where total sales > average sales of all stores


SELECT total_sales.store_id, total_sales.total_sales_per_store
FROM (
    SELECT store_id, SUM(cost_in_cents) AS total_sales_per_store
    FROM products
    GROUP BY store_id
) AS total_sales
JOIN (
    SELECT AVG(total_sales_per_store) AS avg_sales_for_all_stores
    FROM (
        SELECT store_id, SUM(cost_in_cents) AS total_sales_per_store
        FROM products
        GROUP BY store_id
    ) AS x
) AS avg_sales
ON total_sales.total_sales_per_store > avg_sales.avg_sales_for_all_stores;

```

```sql


-- METHOD 2 : Using the WITH Clause

with Total_Sales(store_id ,total_sales_per_store) as 
       
       (select s.store_id , sum(cost_in_cents) as total_sales_per_store from products s group by store_id) ,
       
       Avg_Sales(avg_sales_for_all_stores) as 
       
       (select avg(total_sales_per_store) as avg_sales_for_all_stores from Total_Sales)
       
select * from Total_Sales t1 
join Avg_Sales  t2
on t1.total_sales_per_store > t2.avg_sales_for_all_stores;

```
