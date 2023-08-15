# LEETCODE 45 MOST ASKED

## 175. Combine Two Tables

    select t1.firstName , t1.lastName , t2.city , t2.state from Person t1
  
    LEFT JOIN Address t2 
  
    on t1.personId = t2.personId  ;


## 176. Second Highest Salary

    SELECT MAX(SALARY) FROM employees 
    WHERE SALARY < (SELECT MAX(SALARY) FROM employees);

        OR

    SELECT DISTINCT
        (SELECT DISTINCT Salary
         FROM Employee
         ORDER BY Salary DESC
         LIMIT 1 OFFSET 1) AS SecondHighestSalary;


## 177. Nth Highest Salary

    CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
    	BEGIN
    	SET N = N - 1;
    	  RETURN (
    		  # Write your MySQL query statement below.
    
    		  SELECT DISTINCT Salary
    		  FROM Employee
    		  ORDER BY Salary DESC
    		  LIMIT 1 OFFSET N
    		  
    	  );
    	END



## 178. Rank Scores

    select score , dense_rank() over( order by score desc ) as 'Rank' from Scores;


## 180. Consecutive Numbers


    with temp(num, next_num,prev_num) as
    
    (select num , LEAD(num,1,0) over(order by id) as next_num , LAG(num ,1, 0) over(order by id) as prev_num from LOGS)
    
    select distinct num as ConsecutiveNums from temp
    where num= next_num and num = prev_num;



## 181. Employees Earning More Than Their Managers

    select e1.name AS Employee
    from Employee e1
    join Employee e2
    on e1.managerId = e2.id
    where e1.salary > e2.salary

## 182. Duplicate Emails

    select email as Email from person
    group by email having count(email) > 1;
    

## 183. Customers Who Never Order


    SELECT c.name AS Customers 
    FROM Customers c LEFT JOIN Orders o 
    ON c.id=o.customerId 
    WHERE o.customerId IS NULL;


    
## 184. Department Highest Salary


    SELECT 
    Department.name AS Department ,
    Employee.name AS Employee, 
    Employee.salary
    
    FROM Department  
    
    Inner JOIN Employee  ON Employee.departmentId=Department.id 
    
    WHERE(departmentId, salary) IN
    (SELECT departmentId,MAX(salary) FROM Employee GROUP BY departmentId) ;



## 185. Department Top Three Salaries


    
    SELECT Department, employee, salary FROM (
        SELECT d.name AS Department
            , e.name AS employee
            , e.salary
            , DENSE_RANK() OVER (PARTITION BY d.name ORDER BY e.salary DESC) AS drk
        FROM Employee e JOIN Department d ON e.DepartmentId= d.Id
    ) t WHERE t.drk <= 3

-- 196. Delete Duplicate Emails

    DELETE p1 from person p1 , person p2 where p1.email = p2.email and p1.id > p2.id






## 584. Find Customer Referee


    Select name from Customer where referee_id != 2 or referee_id is null;

## 595. Big Countries


    Select name, population ,area from World where area >= 3000000 or population >= 25000000;


## 586. Customer Placing the Largest no. of Orders

    select customer_number  from orders 
    
    group by customer_number order by Count(customer_number) desc limit 1;



## 596. Classes More Than 5 Students

    select class from courses 
    group by class 
    having count(class) >=5
