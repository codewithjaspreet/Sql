## 1 - Olympic Gold Medals Problem | SQL Online Interview Question | Data Analytics | 2 Solutions


* Create the table -- 

```sql

 CREATE TABLE events (
ID int,
event varchar(255),
YEAR INt,
GOLD varchar(255),
SILVER varchar(255),
BRONZE varchar(255)
);

delete from events;

INSERT INTO events VALUES (1,'100m',2016, 'Amthhew Mcgarray','donald','barbara');
INSERT INTO events VALUES (2,'200m',2016, 'Nichole','Alvaro Eaton','janet Smith');
INSERT INTO events VALUES (3,'500m',2016, 'Charles','Nichole','Susana');
INSERT INTO events VALUES (4,'100m',2016, 'Ronald','maria','paula');
INSERT INTO events VALUES (5,'200m',2016, 'Alfred','carol','Steven');
INSERT INTO events VALUES (6,'500m',2016, 'Nichole','Alfred','Brandon');
INSERT INTO events VALUES (7,'100m',2016, 'Charles','Dennis','Susana');
INSERT INTO events VALUES (8,'200m',2016, 'Thomas','Dawn','catherine');
INSERT INTO events VALUES (9,'500m',2016, 'Thomas','Dennis','paula');
INSERT INTO events VALUES (10,'100m',2016, 'Charles','Dennis','Susana');
INSERT INTO events VALUES (11,'200m',2016, 'jessica','Donald','Stefeney');
INSERT INTO events VALUES (12,'500m',2016,'Thomas','Steven','Catherine');

```

## -- Write a query to find no of gold medal per swimmer for swimmers who won only gold medals




* Method 1 - using subquery - 

```sql

    select Gold , count(Gold) as cnt_gold_medal_per_swimmer from events 
    where Gold not in (select silver from events union all select Bronze from events)
    group by Gold;

```
  
* Method 2 - Group By having + CTE

```sql

with cte as (
SELECT GOLD, count(*) as total_medals
FROM events 
GROUP BY GOLD ) 

select 
GOLD, total_medals 
from cte 
where GOLD NOT IN (select distinct SILVER from events union all select distinct BRONZE from events)


```

---
---

## 2 - Solving a REAL Business Use Case Using SQL | Business Days Excluding Weekends and Public Holidays  - MYSQL

* Create Required Tables -

 ```sql
create table tickets
(
ticket_id varchar(10),
create_date date,
resolved_date date
);
insert into tickets values
(1,'2022-08-01','2022-08-03')
,(2,'2022-08-01','2022-08-12')
,(3,'2022-08-01','2022-08-16');
create table holidays
(
holiday_date date
,reason varchar(100)
);
insert into holidays values
('2022-08-11','Rakhi'),('2022-08-15','Independence day');


select * from tickets;
select * from holidays;

```

* Query to Calculate no of days between two days and then difference of weeks between two days ---

 ```sql

 select * , datediff(resolved_date ,create_date) as no_of_days  , 
 week(create_date) as start_week , week(resolved_date) as end_date ,  FLOOR( datediff(resolved_date ,create_date) / 7) AS  
 weeks_difference
 from tickets ;
 
 ```


* Query gives no of business days excluding weekend between two dates and the public holidays 


```sql

select *  ,  DATEDIFF(resolved_date , create_date) as actual_days,DATEDIFF(resolved_date, create_date) - 2 * FLOOR(DATEDIFF(resolved_date, create_date) / 7) as actual_after_weekend_excluson ,
 DATEDIFF(resolved_date, create_date) - 2 * FLOOR(DATEDIFF(resolved_date, create_date) / 7) - no_of_public_holidays AS actual_after_weekend_and_public_holiday_exclusin
 from (
SELECT ticket_id , create_date , resolved_date , count(holiday_date) as no_of_public_holidays
FROM 
    tickets left join holidays on holiday_date between create_date and resolved_date group by ticket_id , create_date , resolved_date
) as a;

```



## 3 - Amazon SQL Interview Question for Data Analyst Position [2-3 Year Of Experience ] | Data Analytics

* Create the Required Tables - 

```sql

create table hospital ( emp_id int
, action varchar(10)
, time datetime);

insert into hospital values ('1', 'in', '2019-12-22 09:00:00');
insert into hospital values ('1', 'out', '2019-12-22 09:15:00');
insert into hospital values ('2', 'in', '2019-12-22 09:00:00');
insert into hospital values ('2', 'out', '2019-12-22 09:15:00');
insert into hospital values ('2', 'in', '2019-12-22 09:30:00');
insert into hospital values ('3', 'out', '2019-12-22 09:00:00');
insert into hospital values ('3', 'in', '2019-12-22 09:15:00');
insert into hospital values ('3', 'out', '2019-12-22 09:30:00');
insert into hospital values ('3', 'in', '2019-12-22 09:45:00');
insert into hospital values ('4', 'in', '2019-12-22 09:45:00');
insert into hospital values ('5', 'out', '2019-12-22 09:40:00');

select * from hospital;


```


# Write a sql to find the total number of people present inside the hospital


* Method 1 - Using CTE or having clause simply

* Logic - calculate maximum intime and outtime for each employee then you can compare if that max(intime) > max(outtime) this means person is inside hospital


```sql

with cte as (
select emp_id ,max( case when action = 'in' then time  end ) as intime , max(case when action = 'out' then time end )as outtime 

 from hospital group by emp_id)  
 
 select * from cte where intime > outtime or outtime is null;

```

 
 * or
   
```sql
 
 select emp_id ,max( case when action = 'in' then time  end ) as intime , max(case when action = 'out' then time end )as outtime 

 from hospital group by emp_id having  max( case when action = 'in' then time  end )  > max(case when action = 'out' then time end ) 
 
 or
 
 max(case when action = 'out' then time end ) is null ;
 
 ```



* Using JOINS - Seperate both Intime and Outimes

```sql

 with intime as (
  
   select emp_id , max( time ) as latest_intime   from hospital where action = 'in' group by emp_id 
 ), 
 
 outime as (
    select  emp_id , max( time ) as latest_outtime   from hospital where action = 'out' group by emp_id

 )
 
 select * from intime left join outime on  intime.emp_id = outime.emp_id
 where latest_intime > latest_outtime  or latest_outtime is null;
 
 ```


 
 * Magic solution -- we can check if the maximum time consedering in and out   =  maximum time when he/she was in , it means person is 
   inside right now

 ```sql

 with latest_time as (
 
    select emp_id , max(time) as max_latest_time from hospital group by emp_id
 ),
 
 latest_in_time as (  
   
   select emp_id  , max(time) as max_in_time  from hospital  where action  = 'in'  group by emp_id
 
 )
 
 select * from  latest_time lt inner join latest_in_time mt on lt.emp_id = mt.emp_id 
 where max_latest_time = max_in_time;

```

---
---
 
 


 
