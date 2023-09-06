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

---
---

## Referring More advance topics such as Views , Stored Procedures , Indexes , Query Optimisations  from here - [Don't Click here](https://roadmap.sh/sql)

---
---

##  Stored Procedures  - A SQL stored procedure is a set of SQL code that can be saved and reused. In other words, it’s a precompiled object because it’s compiled at a time when it’s created on the database. 

* Setup : 

```sql


 create table mobile_product( product_id int , product_code varchar(5) ,product_name varchar(10),
 price int, quantity_remain int , quantity_sold int);

insert into mobile_product values (1, "P1" ,"Iphone", 20000 ,4,195);


create table sales(order_id int, order_date varchar(10) , product_code varchar(10) , quantity_ordered int , sales_price int);

-- Inserting the first row
INSERT INTO sales (order_id, order_date, product_code, quantity_ordered, sales_price)
VALUES (1, '2023-09-06', 'P1', 10, 50);

-- Inserting the second row
INSERT INTO sales (order_id, order_date, product_code, quantity_ordered, sales_price)
VALUES (2, '2023-09-07', 'P1', 15, 75);

-- Inserting the third row
INSERT INTO sales (order_id, order_date, product_code, quantity_ordered, sales_price)
VALUES (3, '2023-09-08', 'P1', 8, 40);


select * from mobile_product;
select * from sales;


call retail();

```


* Non parameterized Procedure a Sample Example on sales and product table ~ (MySql RDMS Version) --


```sql

DELIMITER $$
DROP PROCEDURE IF EXISTS retail$$

CREATE PROCEDURE retail()
BEGIN
    DECLARE t_product_code VARCHAR(10);
    DECLARE t_product_price FLOAT;
    
    SELECT product_code, price
    INTO t_product_code, t_product_price
    FROM mobile_product
    WHERE product_name = 'Iphone';
    
    INSERT INTO sales (order_id, order_date, product_code, quantity_ordered, sales_price)
    VALUES (1, CURDATE(), t_product_code, 1, (t_product_price * 1));
    
    SET SQL_SAFE_UPDATES=0;

    UPDATE mobile_product
    SET quantity_remain = (quantity_remain - 1), quantity_sold = (quantity_sold + 1)
    WHERE product_code = t_product_code;
    
    SET SQL_SAFE_UPDATES=1;

    SELECT 'Product Sold !';
END$$

DELIMITER ;

```


## Please Make sure to checkout other readme's for Interview Problems , content is regularly updating so stay tuned !!
     
     

