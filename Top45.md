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

