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


---
---

# 9 -  SQL Interview Question Based on Full Outer Join | Asked in Deloitte


# Create required tables 

```sql

create table emp_2020
(
emp_id int,
designation varchar(20)
);

create table emp_2021
(
emp_id int,
designation varchar(20)
);

insert into emp_2020 values (1,'Trainee'), (2,'Developer'),(3,'Senior Developer'),(4,'Manager');
insert into emp_2021 values (1,'Developer'), (2,'Developer'),(3,'Manager'),(5,'Trainee');

select * from emp_2020 ;
select * from emp_2021;

```

* Full outer join dosen't work in mysql so make a union between left and right join

```sql

select e20.* , e21.*  from emp_2020 e20  
left  join emp_2021 e21  on e20.emp_id = e21.emp_id

union

select e20.* , e21.*  from emp_2020 e20  
right  join emp_2021 e21  on e20.emp_id = e21.emp_id;

```

* Query - ( Used combination of joins as full outer join not works in Mysql)

``` sql

select ifnull(e20.emp_id , e21.emp_id) as emp_id , case 
when e21.designation != e20.designation then 'Promoted'
when e21.designation is null then 'Resigned'
else 'New' end 
as comment

from emp_2020 e20  

left join emp_2021 e21 on e20.emp_id = e21.emp_id where ifnull(e20.designation, 'xxx') != ifnull(e21.designation, 'yyy')

union

select ifnull(e20.emp_id , e21.emp_id) as emp_id , case 
when e21.designation != e20.designation then 'Promoted'
when e21.designation is null then 'Resigned'
else 'New' end 
as comment

from emp_2020 e20  

right join emp_2021 e21 on e20.emp_id = e21.emp_id where ifnull(e20.designation, 'xxx') != ifnull(e21.designation, 'yyy');

```

---
---


# 10 - A Simple and Tricky SQL Question | Rank Only Duplicates | SQL Interview Questions

``` sql

create table list (id varchar(5));
insert into list values ('a');
insert into list values ('a');
insert into list values ('b');
insert into list values ('c');
insert into list values ('c');
insert into list values ('c');
insert into list values ('d');
insert into list values ('d');
insert into list values ('e');

select * from list;

```

``` sql
with cte_duplicates as (
select * from list group by id having count(1)>1 ),
cte_rank as (
select id,rank() over(order by id) as rnk from cte_duplicates )
select l.id, cr.rnk  from list l left join cte_rank cr on l.id = cr.id

```

---
---


# 11 - Master SQL UPDATE Statement | SQL UPDATE A-Z Tutorial | SQL Update with JOIN


```sql 
create database ankit_sql;

CREATE TABLE EmployeeData (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(255),
    salary DECIMAL(10, 2),
    manager_id INT,
    emp_age INT,
    dept_id INT
);

INSERT INTO EmployeeData (emp_id, emp_name, salary, manager_id, emp_age, dept_id)
VALUES
    (1, 'John Doe', 60000.00, NULL, 30, 101),
    (2, 'Jane Smith', 55000.00, 1, 28, 101),
    (3, 'Michael Johnson', 70000.00, 1, 32, 102),
    (4, 'Emily Davis', 62000.00, 1, 29, 102),
    (5, 'William Brown', 58000.00, 3, 31, 103),
    (6, 'Olivia Wilson', 56000.00, 3, 27, 103),
    (7, 'James Taylor', 75000.00, 1, 35, 104),
    (8, 'Sophia Martinez', 63000.00, 7, 30, 104),
    (9, 'Alexander Anderson', 60000.00, 7, 29, 105),
    (10, 'Ava Rodriguez', 57000.00, 7, 28, 105);
    
    
CREATE TABLE DepartmentData (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(255)
);

INSERT INTO DepartmentData (dept_id, dept_name)
VALUES
    (101, 'Human Resources'),
    (102, 'Marketing'),
    (103, 'Engineering'),
    (104, 'Finance'),
    (105, 'Sales');


select * from EmployeeData;
select * from DepartmentData;

```



# Update syntax for single value update


```sql

SET SQL_SAFE_UPDATES = 0;

update EmployeeData set salary = 10000 ;

```


# Update syntax with where clause


```sql


update EmployeeData set salary = 12000 where emp_age> 30  ;


```

# Update multiple values

```sql

update  EmployeeData set salary = 12000 , dept_id = 104 where emp_id =  32 ;


```

# Update col with constant values or derived calculations - ( aggregations / case when statement )

```sql


update EmployeeData set salary = case when dept_id = 101 then salary*1.1  when dept_id = 104  then salary*1.2 else salary end;



```


# Update statement using join - here we will see how to join dept name from DepartmentData table to EmployeeData ( Workbench - MySql)


* Adding a new column dept_name in employee table - initially all values will be null

```sql

alter table EmployeeData add dept_name varchar(20);

```

# Update using join 

```sql

update EmployeeData e
inner join DepartmentData d on e.dept_id=d.dept_id set e.dept_name=d.dept_name;


```

# Interview question on swapping the genders - 

* let's first add a gender column in EmployeeData Table

```sql

alter table EmployeeData add gender varchar(15);

```

```sql

update employeedata set gender = case when dept_id = 101 then 'Male' when dept_id = 103 then 'Female' else 'Male' end;
select * from employeedata;

```


# Swap gender ~

```sql


update employeedata set gender = case when gender = 'Male' then 'Female' when gender = 'Female' then 'Male' end;


```

* note - here above we can't use two update statements as - neccessity is to use  - case when for desired output



# Steps to check before making updating the database - convert it into select statement to get a run time overview  -

```sql

select * , case when gender = 'Male' then 'Female' when gender = 'Female' then 'Male' end  as updated_gender from employeedata;

```

---
---


# 12 - Slowly Changing Dimensions In Data warehousing with iPhone 11 Example | SCD 1/2/3

* Watch video for expalanation - [Link](https://youtu.be/ejdIgYPfcV4?si=Ro5YAye2fWMO06mb)

---
---

# 13 -  Custom Sort Trick in SQL | Sorting Happiness Index Data with India on Top | SQL Interview Question


```sql

 /* CREATE TABLE */
CREATE TABLE IF NOT EXISTS TABLE_NAME(
Ranks INT(11),
Country VARCHAR( 100 ),
Happiness_2021  DECIMAL( 10 , 2 ),
Happiness_2020 DECIMAL( 10 , 2 ),
2022_Population INT(11)
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    1,'Finland',7.842,7.809,5554960
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    2,'Denmark',7.62,7.646,5834950
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    3,'Switzerland',7.571,7.56,8773637
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    4,'Iceland',7.554,7.504,345393
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    5,'Netherlands',7.464,7.449,17211447
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    6,'Norway',7.392,7.488,5511370
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    7,'Sweden',7.363,7.353,10218971
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    8,'Luxembourg',7.324,7.238,642371
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    9,'New Zealand',7.277,7.3,4898203
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    10,'Austria',7.268,7.294,9066710
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    11,'Australia',7.183,7.223,26068792
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    12,'Israel',7.157,7.129,8922892
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    13,'Germany',7.155,7.076,83883596
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    14,'Canada',7.103,7.232,38388419
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    15,'Ireland',7.085,7.129,5020199
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    16,'Costa Rica',7.069,7.121,5182354
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    17,'United Kingdom',7.064,7.165,68497907
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    18,'Czech Republic',6.965,6.911,10736784
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    19,'United States',6.951,6.94,334805269
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    20,'Belgium',6.834,6.864,11668278
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    21,'France',6.69,6.664,65584518
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    22,'Bahrain',6.647,6.227,1783983
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    23,'Malta',6.602,6.773,444033
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    24,'Taiwan',6.584,6.455,23888595
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    25,'United Arab Emirates',6.561,6.791,10081785
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    26,'Saudi Arabia',6.494,6.406,35844909
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    27,'Spain',6.491,6.401,46719142
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    28,'Italy',6.483,6.387,60262770
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    29,'Slovenia',6.461,6.363,2078034
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    30,'Guatemala',6.435,6.399,18584039
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    31,'Uruguay',6.431,6.44,3496016
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    32,'Singapore',6.377,6.377,5943546
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    33,'Slovakia',6.331,6.281,5460193
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    34,'Brazil',6.33,6.376,215353593
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    35,'Mexico',6.317,6.465,131562772
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    36,'Jamaica',6.309,5.89,2985094
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    37,'Lithuania',6.255,6.215,2661708
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    38,'Cyprus',6.223,6.159,1223387
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    39,'Estonia',6.189,6.022,1321910
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    40,'Panama',6.18,6.305,4446964
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    41,'Uzbekistan',6.179,6.258,34382084
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    42,'Chile',6.172,6.228,19250195
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    43,'Poland',6.166,6.186,37739785
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    44,'Kazakhstan',6.152,6.058,19205043
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    45,'Romania',6.14,6.124,19031335
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    46,'Kuwait',6.106,6.102,4380326
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    47,'Serbia',6.078,5.778,8653016
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    48,'El Salvador',6.061,6.348,6550389
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    49,'Mauritius',6.049,6.101,1274727
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    50,'Latvia',6.032,5.95,1848837
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    51,'Colombia',6.012,6.163,51512762
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    52,'Hungary',5.992,6,9606259
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    53,'Thailand',5.985,5.999,70078203
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    54,'Nicaragua',5.972,6.137,6779100
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    55,'Japan',5.94,5.871,125584838
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    57,'Portugal',5.929,5.911,10140570
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    56,'Argentina',5.929,5.975,46010234
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    58,'Honduras',5.919,5.953,10221247
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    59,'Croatia',5.882,5.505,4059286
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    60,'Philippines',5.88,6.006,112508994
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    61,'South Korea',5.845,5.872,51329899
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    62,'Peru',5.84,5.797,33684208
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    63,'Bosnia And Herzegovina',5.813,5.674,3249317
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    64,'Moldova',5.766,5.608,4013171
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    65,'Ecuador',5.764,5.925,18113361
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    66,'Kyrgyzstan',5.744,5.542,6728271
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    67,'Greece',5.723,5.515,10316637
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    68,'Bolivia',5.716,5.747,11992656
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    69,'Mongolia',5.677,5.456,3378078
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    70,'Paraguay',5.653,5.692,7305843
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    71,'Montenegro',5.581,5.546,627950
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    72,'Dominican Republic',5.545,5.689,11056370
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    73,'Belarus',5.534,5.54,9432800
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    75,'Hong Kong',5.477,5.51,7604299
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    74,'Russia',5.477,5.546,145805947
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    76,'Tajikistan',5.466,5.556,9957464
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    77,'Vietnam',5.411,5.353,98953541
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    78,'Libya',5.41,5.489,7040745
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    79,'Malaysia',5.384,5.384,33181072
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    80,'Indonesia',5.345,5.286,279134505
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    81,'Republic of the Congo',5.342,5.194,5797805
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    82,'China',5.339,5.124,1448471400
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    83,'Ivory Coast',5.306,5.233,27742298
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    84,'Armenia',5.283,4.677,2971966
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    85,'Nepal',5.269,5.137,30225582
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    86,'Bulgaria',5.266,5.102,6844597
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    87,'Maldives',5.198,5.198,540985
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    88,'Azerbaijan',5.171,5.165,10300205
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    89,'Cameroon',5.142,5.085,27911548
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    90,'Senegal',5.132,4.981,17653671
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    91,'Albania',5.117,4.883,2866374
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    92,'North Macedonia',5.101,5.16,2081304
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    93,'Ghana',5.088,5.148,32395450
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    94,'Niger',5.074,4.91,26083660
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    95,'Turkmenistan',5.066,5.119,6201943
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    96,'Gambia',5.051,4.751,2558482
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    97,'Benin',5.045,5.216,12784726
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    98,'Laos',5.03,4.889,7481023
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    99,'Bangladesh',5.025,4.833,167885689
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    100,'Guinea',4.984,4.949,13865691
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    101,'South Africa',4.956,4.814,60756135
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    102,'Turkey',4.948,5.132,85561976
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    103,'Pakistan',4.934,5.693,229488994
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    104,'Morocco',4.918,5.095,37772756
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    105,'Venezuela',4.892,5.053,29266991
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    106,'Georgia',4.891,4.673,3968738
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    107,'Algeria',4.887,5.005,45350148
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    108,'Ukraine',4.875,4.561,43192122
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    109,'Iraq',4.854,4.785,42164965
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    110,'Gabon',4.852,4.829,2331533
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    111,'Burkina Faso',4.834,4.769,22102838
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    112,'Cambodia',4.83,4.848,17168639
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    113,'Mozambique',4.794,4.624,33089461
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    114,'Nigeria',4.759,4.724,216746934
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    115,'Mali',4.723,4.729,21473764
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    116,'Iran',4.721,4.672,86022837
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    117,'Uganda',4.636,4.432,48432863
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    118,'Liberia',4.625,4.558,5305117
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    119,'Kenya',4.607,4.583,56215221
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    120,'Tunisia',4.596,4.392,12046656
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    121,'Lebanon',4.584,4.772,6684849
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    122,'Namibia',4.574,4.571,2633874
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    123,'Myanmar',4.426,4.308,55227143
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    124,'Jordan',4.395,4.633,10300869
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    125,'Chad',4.355,4.423,17413580
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    126,'Sri Lanka',4.325,4.327,21575842
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    127,'Eswatini',4.308,4.308,1184817
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    128,'Comoros',4.289,4.289,907419
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    129,'Egypt',4.283,4.151,106156692
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    130,'Ethiopia',4.275,4.186,120812698
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    131,'Mauritania',4.227,4.375,4901981
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    132,'Madagascar',4.208,4.166,29178077
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    133,'Togo',4.107,4.187,8680837
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    134,'Zambia',4.073,3.759,19470234
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    135,'Sierra Leone',3.849,3.926,8306436
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    136,'India',3.819,3.573,1406631776
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    137,'Burundi',3.775,3.775,12624840
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    138,'Yemen',3.658,3.527,31154867
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    139,'Tanzania',3.623,3.476,63298550
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    140,'Haiti',3.615,3.721,11680283
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    141,'Malawi',3.6,3.538,20180839
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    142,'Lesotho',3.512,3.653,2175699
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    143,'Botswana',3.467,3.479,2441162
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    144,'Rwanda',3.415,3.312,13600464
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    145,'Zimbabwe',3.145,3.299,15331428
    
    
);

/* INSERT QUERY */
INSERT INTO TABLE_NAME( Ranks,Country,Happiness_2021 ,Happiness_2020,2022_Population )
VALUES
(
    146,'Afghanistan',2.523,2.567,40754388
);

```

```sql


select * from table_name;

```

```sql

SELECT *
FROM (
    SELECT *,
           CASE
               WHEN country = 'India' THEN 1
               WHEN country = 'Pakistan' THEN 2
               WHEN country = 'Sri Lanka' THEN 3
               ELSE 0
           END AS country_derived
    FROM table_name
) temp
ORDER BY country_derived DESC, Happiness_2020 DESC;

```


# Also we can directly give in order by rather than subquery


```sql

select * from table_name 

order by CASE
               WHEN country = 'India' THEN 1
               WHEN country = 'Pakistan' THEN 2
               WHEN country = 'Sri Lanka' THEN 3
               ELSE 0
           END   desc , Happiness_2020 desc;

```


# 14 -   Problem with Running SUM in SQL | Watch it to Avoid The Mistake


```sql 
create table products (product_id Varchar(2) , cost INT);

insert into products values('P1' , 200);
insert into products values('P2' , 300);
insert into products values('P3' , 300);
insert into products values('P4' , 500);
insert into products values('P5' , 600);

select * from products;

```


```sql


select *, sum(cost) over(order by cost) as running_sum from products;

```


* output of this give running sum as same for value 300 - as it is duplicate

* to avoid this and get correct result there are two methods below -- 

* 1- First could be specifying any other column in the order by too in order to get unique result such that product_id in this case

```sql

 select *, sum(cost) over(order by cost, product_id) as running_sum from products;

```

* 2 - or using. - rows between unbounded preceding and current row - clause

```sql

 select *, sum(cost) over(order by cost asc rows between unbounded preceding and current row) as running_sum from products;


```

# 15 - Difference Between count(*) ,count(0),count(-1),count(col),count('youtube') | SQL Interview question

```sql

count(*) - give count of total no. of rows in the table
count(0) , count(-1) , count("jaspreet") ... anything like this means count this constant value count(*) no. of times which is same as count(*)

count(dept_name) .. means count over a column name .. give count of row expect null ones

```
---
---


## 16 - SQL to Count Occurrence of a Character/Word in a String

```sql
create table strings (name varchar(50));
delete from strings;
insert into strings values ('Ankit Bansal'),('Ram Kumar Verma'),('Akshay Kumar Ak k'),('Rahul');


select * from strings;
```

* Query to count no of spaces in the string -- 

```sql
select name , replace( name , ' ' , '') as rep_name , length(name) - length(replace( name , ' ' , '')) as cnt from strings;

```

* Query to count no of occurrences of 'Ak' in the strings -- this is a sample use case

```sql

SELECT
   name,
   REPLACE(name, 'Ak', '') AS rep_name,
   ROUND((LENGTH(name) - LENGTH(REPLACE(name, 'Ak', ''))) / LENGTH('Ak')) AS cnt
FROM strings;

```

---
---
