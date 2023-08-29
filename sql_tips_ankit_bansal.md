# SQL TIPS & TRICKS BY ANKIT BANSAL


## 1 -  Where Clause vs Having Clause

# Let's first Create an Employee Table 

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
  

## 2 - SQL Convert Rows to Columns and Columns to Rows without using Pivot Functions

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

-- 3 - Top 10 SQL interview Questions and Answers | Frequently asked SQL interview questions.

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

## Q8. self join , manager salary > emp salary

## Q9. Joins left Join/ Inner Join

## Q10. Update query to swap gender
