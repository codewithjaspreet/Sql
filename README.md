



# Complete Sql 2022


![sql](https://user-images.githubusercontent.com/85099922/187137617-bf95cb75-57d7-43cf-b253-a67bba04cde1.png)


[Official Website](https://www.mysql.com/)


# Installation

## Get Started

```bash
  https://dev.mysql.com/downloads/installer/
  
```
    
## Lessons you will Learn

Checkout the important topics below for SQl related to placements



## Topic List

#### Definition

```
  SQL: Structured Query Language, used to access and manipulate data
  SQL used CRUD operations to communicate with DB.
```
---


Created Database with three tables :
---

        create database ORG;
        show databases;
        use ORG;

        create table worker(
        WORKER_ID INT NOT NULL PRIMARY KEY auto_increment,
        FIRST_NAME CHAR(25),
        LAST_NAME CHAR(25),
        SALARY INT (15),
        JOINING_DATE datetime,
        DEPARTMENT CHAR(25)
        );
        INSERT INTO Worker 
          (WORKER_ID, FIRST_NAME, LAST_NAME, SALARY, JOINING_DATE, DEPARTMENT) VALUES
            (001, 'Monika', 'Arora', 100000, '14-02-20 09.00.00', 'HR'),
            (002, 'Niharika', 'Verma', 80000, '14-06-11 09.00.00', 'Admin'),
            (003, 'Vishal', 'Singhal', 300000, '14-02-20 09.00.00', 'HR'),
            (004, 'Amitabh', 'Singh', 500000, '14-02-20 09.00.00', 'Admin'),
            (005, 'Vivek', 'Bhati', 500000, '14-06-11 09.00.00', 'Admin'),
            (006, 'Vipul', 'Diwan', 200000, '14-06-11 09.00.00', 'Account'),
            (007, 'Satish', 'Kumar', 75000, '14-01-20 09.00.00', 'Account'),
            (008, 'Geetika', 'Chauhan', 90000, '14-04-11 09.00.00', 'Admin');

                Select * from Worker;

        CREATE TABLE Bonus(

        WORKER_REF_ID INT,
        BONUS_AMOUNT INT(10),
        BONUS_DATE DATETIME,
        foreign key(WORKER_REF_ID)
           references Worker(WORKER_ID)
           ON delete cascade
        );

        INSERT INTO Bonus 
          (WORKER_REF_ID, BONUS_AMOUNT, BONUS_DATE) VALUES
            (001, 5000, '16-02-20'),
            (002, 3000, '16-06-11'),
            (003, 4000, '16-02-20'),
            (001, 4500, '16-02-20'),
            (002, 3500, '16-06-11');

        create table Title(

        WORKER_REF_ID int,
        WORKER_TITLE char(25),
        AFFECTED_FROM datetime,
        foreign key (WORKER_REF_ID)
        references Worker(WORKER_ID)
        on delete cascade
        );


        INSERT INTO Title 
          (WORKER_REF_ID, WORKER_TITLE, AFFECTED_FROM) VALUES
         (001, 'Manager', '2016-02-20 00:00:00'),
         (002, 'Executive', '2016-06-11 00:00:00'),
         (008, 'Executive', '2016-06-11 00:00:00'),
         (005, 'Manager', '2016-06-11 00:00:00'),
         (004, 'Asst. Manager', '2016-06-11 00:00:00'),
         (007, 'Executive', '2016-06-11 00:00:00'),
         (006, 'Lead', '2016-06-11 00:00:00'),
         (003, 'Lead', '2016-06-11 00:00:00');

         select * from Title
---
Questions

                    Q-1. Write an SQL query to fetch “FIRST_NAME” from Worker table using the alias name as <WORKER_NAME>.
                    select first_name AS WORKER_NAME from worker;
                       
                    -- solution 1 : 
                    select FIRST_NAME from Worker;




