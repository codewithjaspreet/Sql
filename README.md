# Complete Sql 2023

# Advanced SQL Mastery :crystal_ball:

Welcome to my repository where I delve into advanced SQL topics. This project is a hub for discovering the intricacies of SQL through practical examples and straightforward explanations.

## What's Covered?

In this repository, you'll find basic yet insightful demonstrations of various advanced SQL concepts. From window functions to other advanced operations, we'll walk through fundamental techniques step by step.

Let's embark on this SQL journey together! :rocket:


# WINDOW FUNCTIONS ~

```sql
-- Journey into Row Number Magic :star:

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

```sql
-- Ascend the Rank Tower :trophy:

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

```sql
-- Unveiling the Secrets of Lead and Lag :crystal_ball:

-- Peer into the past and future with the mystic `LEAD()` and `LAG()` incantations, unlocking the wisdom of adjacent records:

SELECT e.*,
       LAG(salary) OVER (PARTITION BY dept_name ORDER BY emp_id) AS prev_emp_salary,
       LEAD(salary) OVER (PARTITION BY dept_name ORDER BY emp_id) AS next_emp_salary
FROM employees e;

```



