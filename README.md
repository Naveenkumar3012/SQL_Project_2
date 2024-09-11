# Bike Rental Shop - SQL Case Study

## Case Study URL
 This case study is taken from [LearnSQL.com](https://learnsql.com/). It is part of the "November 2023 SQL Challengeˮ course 

## Objective

Emily is the shop owner, and she would like to gather data to help her grow the 
business. She has hired you as an SQL specialist to get the answers to her 
business questions such as How many bikes does the shop own by category? 
What was the rental revenue for each month? etc.

## Schema

Check (Bike_rental_Schema.sql) File in the Project Repository

- **Note:** RDBMS used - PostgreSQL

##  Problem Statements
 Following are the business questions that Emily wants answers to.
 
![](https://github.com/Naveenkumar3012/SQL_Project_2/blob/main/Problem%20Statement%201.jpeg)
![](https://github.com/Naveenkumar3012/SQL_Project_2/blob/main/Problem%20Statement%202.jpeg)
![](https://github.com/Naveenkumar3012/SQL_Project_2/blob/main/Problem%20Statement%203.jpeg)

## Solutions
Problem 1 :
```sql
select category, count(1) as number_of_bikes
from bike
group by category
having count(1) > 2
```

Problem 2:
```sql
select c.name, count(m.id) as membership_count 
from membership m
right join customer c on c.id=m.customer_id
group by c.name
order by count(1) desc;
```

Problem 3:
```sql
select id, category
, price_per_hour as old_price_per_hour
, case when category = 'electric' then round(price_per_hour - (price_per_hour*0.1) ,2)
	   when category = 'mountain bike' then round(price_per_hour - (price_per_hour*0.2) ,2)
       else round(price_per_hour - (price_per_hour*0.5) ,2)
  end as new_price_per_hour
, price_per_day as old_price_per_day
, case when category = 'electric' then round(price_per_day - (price_per_day*0.2) ,2)
	   when category = 'mountain bike' then round(price_per_day - (price_per_day*0.5) ,2)
       else round(price_per_day - (price_per_day*0.5) ,2)
  end as new_price_per_day
from bike;
```

Problem 4:
```sql
select category,
count(case when status = 'available' then 1 end) as available_bikes_count,
count(case when status = 'rented' then 1 else 0 end)as rented_bikes_count
from bike
group by category
```

Problem 5:
```sql
select extract(year from start_timestamp) as year
, extract(month from start_timestamp) as month
, sum(total_paid) as revenue
from rental
group by extract(year from start_timestamp), extract(month from start_timestamp)
union all
select extract(year from start_timestamp) as year
, null as month, sum(total_paid) as revenue
from rental
group by extract(year from start_timestamp)
union all
select null as year, null as month, sum(total_paid) as revenue
from rental
order by year, month;
```

Problem 6:
```sql
select extract(year from start_date) as year
, extract(month from start_date) as month
, mt.name as membership_type_name
, sum(total_paid) as total_revenue
from membership m
join membership_type mt on m.membership_type_id = mt.id
group by year, month, mt.name
order by year, month, mt.name
```

problem 6(solution 2)
```sql
select extract(year from start_timestamp) as year
, extract(month from start_timestamp) as month
, sum(total_paid) as revenue
from rental
group by rollup(year, month)
order by year, month;
```

Problem 7:
```sql
select mt.name as membership_type_name
, extract(month from start_date) as month
, sum(total_paid) as total_revenue
from membership m
join membership_type mt on m.membership_type_id = mt.id
where extract(year from start_date) = 2023
group by CUBE(membership_type_name, month)
order by membership_type_name, month;
```

Problem 8:
```sql
with cte as 
    (select customer_id, count(1)
    , case when count(1) > 10 then 'more than 10' 
           when count(1) between 5 and 10 then 'between 5 and 10'
           else 'fewer than 5'
      end as category
    from rental
    GROUP by customer_id)
select category as rental_count_category
, count(*) as customer_count
from cte 
group by category
order by customer_count;
```

## Findings and Conclusion

- **Bike Inventory:** The shop has a good number of bikes in most categories, but there might be a need to increase the inventory of certain categories based on demand.
- **Customer Loyalty** Some customers have purchased multiple memberships, indicating high customer loyalty.
- **Rental Revenue:** The shop generates significant revenue from rentals, with certain months and years showing higher revenue than others.
- **Membership Revenue:** Membership sales contribute to the overall revenue, and certain membership types might be more popular than others.
- **Customer Segmentation:** The customer segmentation analysis can help the shop tailor marketing strategies to different customer segments.

By analyzing these data points, the bike rental shop can make informed decisions to improve its operations, increase revenue, and enhance customer satisfaction.
