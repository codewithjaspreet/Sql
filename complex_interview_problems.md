

# Complex SQL 2 | find new and repeat customers | SQL Interview Questions

```sql

create table customer_orders (
order_id integer,
customer_id integer,
order_date date,
order_amount integer
);

insert into 
customer_orders values
(1,100,cast('2022-01-01' as date),2000),
(2,200,cast('2022-01-01' as date),2500),
(3,300,cast('2022-01-01' as date),2100),
(4,100,cast('2022-01-02' as date),2000),
(5,400,cast('2022-01-02' as date),2200),
(6,500,cast('2022-01-02' as date),2700),
(7,100,cast('2022-01-03' as date),3000),
(8,400,cast('2022-01-03' as date),1000),
(9,600,cast('2022-01-03' as date),3000)
;

select * from customer_orders;

```

-- Find New and Repeat Customers on each order date

```sql

with first_order_table as 
(select customer_id, min(order_date) as first_order_date
from customer_orders
group by customer_id)

select co.order_date,
sum(case when co.order_date = fot.first_order_date then co.order_amount else 0 end) as order_amount_by_new_customer,
sum(case when co.order_date <> fot.first_order_date then co.order_amount else 0 end) as order_amount_by_repeat_customer
from customer_orders co 
join first_order_table fot on fot.customer_id = co.customer_id
group by co.order_date
order by 1;

```

# USING ROW_NUMBER FUNCTION -- 

```sql

with order_rnk as (

select order_date , row_number() over(partition by customer_id order by order_id) as rnk from customer_orders

)

select order_date , 
sum(case when rnk = 1 then 1 else 0 end ) as new_customer,

sum(case when rnk > 1 then 1 else 0 end ) as repeat_customer

from order_rnk

group by 1


```

---
---


# Complex SQL 3 | Scenario based Interviews Question for Product companies

```sql

create table entries ( 
name varchar(20),
address varchar(20),
email varchar(20),
floor int,
resources varchar(10));

insert into entries 
values ('A','Bangalore','A@gmail.com',1,'CPU'),('A','Bangalore','A1@gmail.com',1,'CPU'),('A','Bangalore','A2@gmail.com',2,'DESKTOP')
,('B','Bangalore','B@gmail.com',2,'DESKTOP'),('B','Bangalore','B1@gmail.com',2,'DESKTOP'),('B','Bangalore','B2@gmail.com',1,'MONITOR')

;
select  * from entries;

SELECT name, count(*) as number_of_visit, group_concat(distinct resources) as used_resources FROM entries group by name;

```

