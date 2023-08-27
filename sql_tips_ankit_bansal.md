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
  

