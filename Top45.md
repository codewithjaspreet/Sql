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
