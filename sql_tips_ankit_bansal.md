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
