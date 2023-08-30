# SQL TIPS & TRICKS BY ANKIT BANSAL


# 1 -  Where Clause vs Having Clause

## Let's first Create an Employee Table 

```sql

    CREATE TABLE employee (
        emp_id INT PRIMARY KEY AUTO_INCREMENT,
        department_id INT,
        salary DECIMAL(10, 2),
        emp_name VARCHAR(100),
        manager_id INT,
        CONSTRAINT fk_manager
            FOREIGN KEY (manager_id)
            REFERENCES employee(emp_id)
    );

    INSERT INTO employee (department_id, salary, emp_name, manager_id)
    VALUES
        (1, 50000.00, 'John Doe', NULL),
        (1, 60000.00, 'Jane Smith', 1),
        (2, 55000.00, 'Michael Johnson', NULL),
        (2, 70000.00, 'Emily Brown', 3),
        (1, 45000.00, 'Robert Lee', 1),
        (3, 80000.00, 'Jessica Davis', NULL),
        (3, 75000.00, 'William Wilson', 6),
        (2, 60000.00, 'Linda Anderson', 3),
        (1, 48000.00, 'James White', 1),
        (3, 72000.00, 'Karen Martinez', 6);

```

* Where clause use to filter the data row by row - which means the keyword iteratively check for each row according to the filter condition.

```sql
  
select * from employee 
where salary > 60000 ;

```
---

* Having clause is used on aggregated values

```sql


select department_id, avg(salary) from employee
group by department_id having avg(salary) > 30000;

```

---

* while using both keep in mind where should be used first then do having !

```sql


select department_id ,avg(salary) from employee
where salary > 60000 group by department_id having avg(salary)>30000;

```
  
---
---

# 2 - SQL Convert Rows to Columns and Columns to Rows without using Pivot Functions

## Create Table ~

```sql
create table emp_compensation (
emp_id int,
salary_component_type varchar(20),
val int
);
insert into emp_compensation
values (1,'salary',10000),(1,'bonus',5000),(1,'hike_percent',10)
, (2,'salary',15000),(2,'bonus',7000),(2,'hike_percent',8)
, (3,'salary',12000),(3,'bonus',6000),(3,'hike_percent',7);


select * from emp_compensation;

```

## Converting rows to columns, also known as pivoting

```sql
select emp_id,
sum(case when salary_component_type = 'salary' then val end  ) as salary,
sum(case when salary_component_type = 'hike_percent' then val end) as hike_percent,
sum(case when salary_component_type = 'bonus' then val end ) as bonus
from emp_compensation
group by emp_id;

```

## Making a new table generated using above query 

``` sql
create table  emp_compensation_unpivot as 
select emp_id,
sum(case when salary_component_type = 'salary' then val end  ) as salary,
sum(case when salary_component_type = 'hike_percent' then val end) as hike_percent,
sum(case when salary_component_type = 'bonus' then val end ) as bonus
from emp_compensation
group by emp_id;

```

## Converting columns to rows, also known as unpivoting

```sql

select * from(
select emp_id, 'salary' as salary_component_type, salary as val from emp_compensation_unpivot
union all
select emp_id, 'hike_percent' as salary_component_type, hike_percent as val from emp_compensation_unpivot
union all
select emp_id, 'bonus' as salary_component_type, bonus as val from emp_compensation_unpivot

) as temp order by emp_id;

```

---
---
# 3 - Top 10 SQL interview Questions and Answers | Frequently asked SQL interview questions.

## Create the 'emp' table

```sql
CREATE TABLE sampletable (
    emp_id INT,
    emp_name VARCHAR(20),
    department_id INT,
    salary INT,
    manager_id INT,
    emp_age INT
);
```

## Insert data into the 'emp' table

```sql

INSERT INTO sampletable (emp_id, emp_name, department_id, salary, manager_id, emp_age)
VALUES
    (1, 'Ankit', 100, 10000, 4, 39),
    (2, 'Mohit', 100, 15000, 5, 48),
    (3, 'Vikas', 100, 10000, 4, 37),
    (4, 'Rohit', 100, 5000, 2, 16),
    (5, 'Mudit', 200, 12000, 6, 55),
    (6, 'Agam', 200, 12000, 2, 14),
    (7, 'Sanjay', 200, 9000, 2, 13),
    (8, 'Ashish', 200, 5000, 2, 12),
    (9, 'Mukesh', 300, 6000, 6, 51),
    (10, 'Rakesh', 300, 7000, 6, 50);
    
```

## inserting a duplicate

``` sql

    insert into sampletable(emp_id, emp_name, department_id, salary, manager_id, emp_age)
    values( 1, 'Aman', 400, 5000, 2, 50);

```

## table structure

```sql

    select * from sampletable;

```

## Q1. How to find duplicate in a given table

```sql

    select emp_id , count(1) from sampletable group by emp_id having count(1) > 1 ;

```

## Q2. How to delete duplicates ~ (Works in MYSQL Server)

```sql
			
	with cte as (select *,   row_number() over(partition by emp_id  order by emp_id) as rn from sampletable) 
    delete  from cte where rn >1;
    
```

## Q3. Difference between union and union all

* when we do "union all" - between two queries it will give summation of both results , kind of merging the tables
* whereas , when "union" is used - it will remove duplicates and give unique entries as a final result



## Q4. Difference rank , row_number and dense_rank

   * Checkout previous readme's of mine for explanation !
   

## Q5. Employees who are not present in department table

    
* let's us first create our department table
    
 ```sql
    
    -- Create the department table
    
		CREATE TABLE department (
			dept_id INT PRIMARY KEY,
			dept_name VARCHAR(255)
		);
```
* Insert two records

```sql
		INSERT INTO department (dept_id, dept_name) VALUES (100, 'Analytics');
		INSERT INTO department (dept_id, dept_name) VALUES (300, 'IT');


		  select * from sampletable;
		  select * from department;
		  select * from sampletable where department_id not in ( select dept_id from department);

```
  
* or we can use Left join as to optimise the above sub query

```sql
      select sampletable.* , department.dept_id , department.dept_name from sampletable
      left join department on sampletable.department_id = department.dept_id where department.dept_name is null;

```

## Q6. Second highest salary in each department


```sql

	select * from(
	select   sampletable.*,  dense_rank() over(partition by department_id order by salary desc) as rn from sampletable

	) as temp where temp.rn = 2;

```


## Q7. Find all transaction done by shilpa


```sql

 create orders table - 
 CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_name VARCHAR(255),
    order_date DATE,
    order_amount DECIMAL(10, 2),
    customer_gender ENUM('Male', 'Female', 'Other')
);

```

```sql

INSERT INTO orders (customer_name, order_date, order_amount, customer_gender)
VALUES
    ('Shilpa', '2020-01-01', 10000, 'Male'),
    ('Rahul', '2020-01-02', 12000, 'Female'),
    ('SHILPA', '2020-01-02', 12000, 'Male'),
    ('Rohit', '2020-01-03', 15000, 'Female'),
    ('shilpa', '2020-01-03', 14000, 'Male');

```

```sql


select * from orders where UPPER(customer_name) = 'SHILPA';


```

## Q8. self join , manager salary > emp salary

```sql


  select * from sampletable;
  
  select t1.emp_name , t1.salary from sampletable t1 JOIN
  sampletable t2 on t1.manager_id = t2.emp_id where t1.salary > t2.salary;


```

## Q9. Joins left Join/ Inner Join

* Inner Join: Combines matching rows from both tables based on a condition, excluding non-matching rows.
* Left Join: Combines all rows from the left table with matching rows from the right table; non-matching rows from the left table have null values.
* Right Join: Combines all rows from the right table with matching rows from the left table; non-matching rows from the right table have null values.


## Q10. Update query to swap gender


```sql

SET SQL_SAFE_UPDATES = 0;

UPDATE orders
SET customer_gender = CASE
    WHEN customer_gender = 'Male' THEN 'Female'
    WHEN customer_gender = 'Female' THEN 'Male'
    ELSE customer_gender  -- Include this line if you want to keep other values as they are
END;

select * from orders;

```

---
---

# 4 -  SQL Self Join Concept | Most Asked Interview Question | Employee Salary More than Manager's Salary


```sql

  select * from sampletable;
  
  select t1.emp_name , t1.salary from sampletable t1 JOIN
  sampletable t2 on t1.manager_id = t2.emp_id where t1.salary > t2.salary;

```

# 5 - How to Practice SQLs Without Creating Tables In Your Database


```sql


with emp1 as
(
select 1 as emp_id, 1000 as emp_salary, 1 as dep_id
union all select 2 as emp_id, 2000 as emp_salary, 2 as dep_id
union all select 3 as emp_id ,3000 as emp_salary, 3 as dep_id
union all select 4 as emp_id ,4000 as emp_salary, 4 as dep_id
),
dep as
(
select 1 as dep_id ,'d1' as dep_name
union all select 2 as dep_id, 'd1' as dep_name
union all select 3 as dep_id, 'd2' as dep_name
union all select 4 as dep_id, 'd3' as dep_name
)
select* from emp;

```
---
---

# 6 - SQL Cross Join | Use Cases | Master Data | Performance Data


## Create Required Tables used in the video - 

```sql

create table products (
id int,
name varchar(10)
);
insert into products VALUES 
(1, 'A'),
(2, 'B'),
(3, 'C'),
(4, 'D'),
(5, 'E');


create table colors (
color_id int,
color varchar(50)
);
insert into colors values (1,'Blue'),(2,'Green'),(3,'Orange');


create table sizes
(
size_id int,
size varchar(10)
);

insert into sizes values (1,'M'),(2,'L'),(3,'XL');


create table transactions
(
order_id int,
product_name varchar(20),
color varchar(10),
size varchar(10),
amount int
);
insert into transactions values (1,'A','Blue','L',300),(2,'B','Blue','XL',150),(3,'B','Green','L',250),(4,'C','Blue','L',250),
(5,'E','Green','L',270),(6,'D','Orange','L',200),(7,'D','Green','M',250);

```

* First use case is to produce master data for a fact table 

```sql


select * from transactions;

select product_name, color, size, sum(amount) as totalamount
from transactions
group by product_name, color, size;

with master_data as (select p.name as product_name ,  c.color , s.size from products p , colors c , sizes s)
, sales as (select product_name, color, size , sum(amount) as totalamount
from transactions
group by product_name, color, size)
select md.product_name, md.color, md.size  , ifnull(s.totalamount,0) as totalamount from master_data md
LEFT join sales  s on md.product_name=s.product_name and md.color=s.color and md.size = s.size order by totalamount;

```

* Second use case is when you want to generate large no of records for performance testing.
* Code shown below is just a example like how we can join table with large records to make a large dataset and manipulate calculations


```sql


select row_number() over (order by t.order_id) as order_id, t.product_name, t. color,
case when row_number() over (order by t.order_id) %3=0 then 'L' else 'XL'end size
,t.amount from transactions t;

```

---
---

# 7 - Most Asked SQL JOIN based Interview Question | # of Records after 4 types of JOINs

## interview question: no of records with diffrent kinds of joins when there are duplicate key values

```sql

create table t1 ( a  INT  ) ;
create table t2 ( b  INT  ) ;

Insert into t1 values(1);
Insert into t1 values(1);

Insert into t2 values(1);
Insert into t2 values(1);
Insert into t2 values(1);

select  * from t1;
select * from t2;

```

* Inner join

```sql

select * from t1 inner join t2 on t1.a = t2.b;

```
* Left Join

```sql

select * from t1 left join t2 on t1.a = t2.b;

```
* Right Join
  
```sql

select * from t1 right join t2 on t1.a = t2.b;

```
* Full Outer Join

```sql
 
select * from t1 full outer join t2 on t1.a = t2.b;

```

* point to be noted null != null , we can't join on this condition

---
---

# 8 - How to Calculate Mode in SQL | How to Find Most Frequent Value in a Column

```sql

create table modes ( temp INT);
insert into modes values(1);
insert into modes values(2);
insert into modes values(2);
insert into modes values(2);
insert into modes values(3);
insert into modes values(4);
insert into modes values(5);
insert into modes values(5);
insert into modes values(7);
insert into modes values(7);
insert into modes values(8);

select * from modes;

```

# Query to find mode - 

* Method 1 - Using CTE -

```sql

with freq_cte as (
select temp , count(*) as freq from modes group by temp) 
select * from freq_cte where freq = (select max(freq) from freq_cte);

```

* Method 2 - Using Rank Function -

```sql

select temp , row_number() over(partition by temp order by temp desc) from modes; 

with freq_cte as ( select temp , count(*) as freq from modes group by temp) ,
rnk_cte as (select * , rank() over(order by freq desc) as rn from freq_cte)
select * from rnk_cte where rn= 1;

```
