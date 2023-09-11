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
 
## 4 - Airbnb SQL Interview Question | Convert Comma Separated Values into Rows | Data Analytics

* Create Required Tables -

 ```sql
create table airbnb_searches 
(
user_id int,
date_searched date,
filter_room_types varchar(200)
);
insert into airbnb_searches values
(1,'2022-01-01','entire home,private room')
,(2,'2022-01-02','entire home,shared room')
,(3,'2022-01-02','private room,shared room')
,(4,'2022-01-03','private room')
;

/*Find the room types that are searched most no of times.
Output the room type alongside the number of searches for it.
If the filter for room types has more than one room type,
consider each unique room type as a separate row.
Sort the result based on the number of searches in descending order.
*/

select * from airbnb_searches;

```

* Query using Cte's and Like Clause ~~ MYSQL

```sql

with room1 as (
select sum(case when filter_room_types like '%entire%' then 1 else 0 end) as s1 from airbnb_searches

),
 room2 as (  select sum(case when filter_room_types like '%private%' then 1 else 0 end) as s2 from airbnb_searches
),
 room3 as  (  select sum(case when filter_room_types like '%shared%' then 1 else 0 end) as s3 from airbnb_searches) 
 
 , final_output  as (
 select 'entire home' as value , s1 as cnt  from room1
 union all
 select 'private room' as value , s2  as cnt from room2
 union all
  select 'shared room' as value , s3 as cnt from room3
  
  
)

select * from final_output order by cnt desc;

```

---
---




 
## 5 - SQL Interview Question for Senior Data Engineer Position in Poland | Data Engineering


* Table Script -

```sql

CREATE TABLE emp_salary
(
    emp_id INTEGER  NOT NULL,
    name NVARCHAR(20)  NOT NULL,
    salary NVARCHAR(30),
    dept_id INTEGER
);


INSERT INTO emp_salary
(emp_id, name, salary, dept_id)
VALUES(101, 'sohan', '3000', '11'),
(102, 'rohan', '4000', '12'),
(103, 'mohan', '5000', '13'),
(104, 'cat', '3000', '11'),
(105, 'suresh', '4000', '12'),
(109, 'mahesh', '7000', '12'),
(108, 'kamal', '8000', '11');

select * from emp_salary;

```

* Write a SQL to return all employee whose salary is same in same department


* This is Using Inner Join ~ 

```sql

with cte as(
select dept_id , salary from emp_salary group by dept_id , salary having count(1) > 1 )

select t1.* from emp_salary t1 inner join cte on t1.dept_id = cte.dept_id where t1.salary = cte.salary;


```

** Alternate using Left Join

 ```sql

with sal_dep as(
select dept_id, salary
from emp_salary
group by dept_id, salary
having count(1)=1)
select es. * from
emp_salary es
LEFT join sal_dep sd on es.dept_id=sd.dept_id and es.salary=sd.salary
where sd.dept_id is null


```
---
---

## 6 & 7  ~ Premium Questions on Datalemur.com

---
---

# 8 - Most Asked Join Based SQL Problem Part -2 | Asked in Amazon for Data Engineer Position


/*There are 2 tables, first table has 5 records and second table has 10 records.
you can assume any values in each of the tables. how many maximum and minimum records possible in case of
inner join, left join, right join and full outer join */


```sql

create table t1 ( val1 int );
create table t2 (val2 int);
insert into t1 values(1), (1), (1), (1), (1);
insert into t2 values(1), (1), (1), (1), (1) , (1), (1), (1), (1), (1);

create table t3(val3 int);
insert into t3 values(2), (2), (2), (2), (2) , (2), (2), (2), (2), (2);


```


* calculation of t1 with t2 produces maximum records in each case of join
* calculation of t3 with t2 produces minimum records in each case of join

* logic is to produce maxium records make the cartesian product maximise by making values as same in both the table
* while to achieve minimum have disimilar values

```sql

select * from t1;
select * from t2;
select * from t3;

select * from t1 inner join t2 on t1.val1 = t2.val2;
select * from t1 left join t2 on t1.val1 = t2.val2;
select * from t1 right join t2 on t1.val1 = t2.val2;

select * from t1 left join t2 on t1.val1 = t2.val2
union all
select * from t1 right join t2 on t1.val1 = t2.val2;


select * from t3 inner join t2 on t3.val3 = t2.val2; -- 0 records
select * from t3 left join t2 on t3.val3= t2.val2;  -- 10 records from t3
select * from t3 right join t2 on t3.val3 = t2.val2; -- 10 records from t2 


select * from t3 left join t2 on t3.val3 = t2.val2
union all
select * from t3 right join t2 on t3.val3 = t2.val2;

```

---
---

## Tricky SQL Interview Problem with a Simple Solution | Data Analytics

* Create Table as required

```sql

create table purchases(user_id int,product_id int,quantity int,purchase_date datetime);
insert into purchases values(536, 3223, 6, '2022-01-11 12:33:44');
insert into purchases values(827, 3585, 35, '2022-02-20 14:05:26');
insert into purchases values(536, 3223, 5, '2022-03-02 09:33:28');
insert into purchases values(536, 1435, 10, '2022-03-02 08:40:00');
insert into purchases values(827, 2452, 45, '2022-03-02 00:00:00');
insert into purchases values(333, 1122, 9, '2022-02-06 01:00:00');
insert into purchases values(333, 1122, 10, '2022-02-06 02:00:00');

select * from purchases;


```


## 9 - Write a sql query to give information of that users who made purchases of same product on different dates

```sql

select user_id, product_id , count(distinct Date(purchase_date)) as p_cnt from purchases 
group by user_id, product_id   having count(distinct Date(purchase_date)) > 1

```

---
---




## Top 5 Artists [Spotify SQL Interview Question]


```sql

/* This CTE1 retrieves the count of song appearances for each artist in the top 10 of the global_song_rank table */
WITH TopArtists AS (
    SELECT
        a.artist_name,
        COUNT(*) AS song_appearances
    FROM
        artists a
    JOIN
        songs s ON a.artist_id = s.artist_id
    JOIN
        global_song_rank gsr ON s.song_id = gsr.song_id
    WHERE
        gsr.rank <= 10
    GROUP BY
        a.artist_name
),
/* This CTE Assigns ranks to the artists based on their song appearances */
RankedArtists AS (
    SELECT
        artist_name,
        song_appearances,
        DENSE_RANK() OVER (ORDER BY song_appearances DESC) AS artist_rank
    FROM
        TopArtists
)
/* Final SELECT statement filters and retrieves the top 5 ranked artists along with their ranks.*/
SELECT
    artist_name,
    artist_rank
FROM
    RankedArtists
WHERE
    artist_rank <= 5
ORDER BY
    artist_rank;

```

# Game Analysis I-1V

```sql

create table activity (

 player_id   int     ,
 device_id     int     ,
 event_date    date    ,
 games_played  int
 );

 insert into activity values (1,2,'2016-03-01',5 ),(1,2,'2016-03-02',6 ),(2,3,'2017-06-25',1 )
 ,(3,1,'2016-03-02',0 ),(3,4,'2018-07-03',5 );

 select * from activity;

```

 
# Questions:

```sql

 Game Play Analysis 

 q1: Write an SQL query that reports the first login date for each player

 q2: Write a SQL query that reports the device that is first logged in for each player

 q3: Write an SQL query that reports for each player and date, how many games played so far by the player. 
     That is, the total number of games played by the player until that date.

 q4: Write an SQL query that reports the fraction of players that logged in again 
     on the day after the day they first logged in, rounded to 2 decimal places

```


* Solution 1 - 

```sql

select player_id , min(event_date) as f_login  from activity group by player_id;

```

* Solution 2 -

```sql

select player_id , min(device_id) as f_device from activity group by player_id ;

```

* Solution 3

```sql

select player_id , sum(games_played) over(partition by  player_id order by event_date) as total_till_date , event_date from activity ;

```


* Solution 4

```sql


WITH PlayerFirstEvent AS (
    SELECT player_id, MIN(event_date) AS event_date
    FROM Activity
    GROUP BY player_id
)

SELECT ROUND(COUNT(DISTINCT b.player_id) / COUNT(DISTINCT a.player_id), 2) AS fraction
FROM PlayerFirstEvent a
LEFT JOIN Activity b ON a.player_id = b.player_id AND DATEDIFF(b.event_date, a.event_date) = 1;

 ```

