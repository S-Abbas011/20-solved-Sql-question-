## 20-solved-Sql-question-
This repository contains 20 SQL practice questions solved using the SuperMart Database. The questions focus on strengthening SQL concepts such as Joins, Common Table Expressions (CTEs), and Subqueries, which are essential for data analysis and reporting tasks.


## 1.Display customer name, product name, and quantity for all sales orders

```SQL
Select c.customer_name, p.product_name, s.quantity 
from customer as c 
join sales as s 
on c.customer_id = s.customer_id 
join product as p
on s.product_id = p.product_id 
```

## 2.Find total sales amount per product category.
```SQL
select p.category, sum(s.sales) from product as p 
join sales as s 
on p.product_id= s.product_id 
group by p.category 
```

## 3.List customers who have spent more than the average spending of all customers.
```SQL
select c.customer_name , 
sum(s.sales) as total_sales from 
customer as c 
join sales as s 
on c.customer_id = s.customer_id 
group by c.customer_name 
having sum(s.sales) > (select avg(sales) from sales )
order by total_sales desc
```

## 4.Using a CTE, calculate total profit per region and show only regions with profit above 50,000.
```SQL
with total_profit_per_region as(
Select c.region, sum(s.profit) total_profit
from customer as c 
join sales as s 
on c.customer_id = s.customer_id 
group by c.region
)
select region, total_profit 
from total_profit_per_region 
where total_profit > 50000
```

## 5.Find top 3 customers based on total purchase amount.
```SQL
select c.customer_name, sum(s.sales) as total_sales
from customer as c 
join sales as s 
on c.customer_id = s.customer_id 
group by c.customer_name
order by total_Sales desc 
limit 3 
```

## 6.List each region and the number of unique customers in it.
```SQL
select region , count(distinct customer_name) as unqiue_cust
from customer
group by region 
```

## 7.Rank products by total sales within each category using a window function.
```SQL
with rank_product as (
select p.product_name,c.region, sum(s.sales) as total_sales
from product as p 
join sales as s 
on p.product_id = s.product_id 
join customer as c 
on c.customer_id = s.customer_id 
group by p.product_name, c.region 
)
select product_name ,total_Sales,
rank() over(partition by region order by total_sales desc) as rank_cust_by_total_sales
from rank_product
order by total_Sales asc
```
## 8.Show all products that have never been sold.

```SQL
select p.product_id , p.product_name , s.order_id 
from product as p 
left join sales as s
on p.product_id = s.product_id 
where s.order_id is NULL
```

## 9.Find monthly sales trends for the last 6 months.
```SQL
select extract(month from order_date) months, sales
from sales
where order_date >= CURRENT_DATE - INTERVAl '6 months'
```

## 10.Find the most profitable product in each region.
```SQL
with most_profitable_product AS (
Select c.region ,p.product_name, sum(s.profit) Total_profit
from customer as c 
join sales as s
on c.customer_id = s.customer_id 
join product as p 
on s.product_id = p.product_id 
group by c.region, p.product_name
order by total_profit desc
)
SELECT region, product_name,Total_profit 
from most_profitable_product

```
## 11.Display total quantity sold per customer per category.

```SQL
select c.customer_name, p.category, sum(s.quantity) as Total_quantity
from customer as c 
join sales as s
on c.customer_id = s.customer_id 
join product as p 
on s.product_id = p.product_id 
group by c.customer_name, p.category
order by c.customer_name 
```
## 12.Find the category contributing the highest revenue overall
```SQL
Select p.category , sum(s.sales * s.quantity) as total_Revenue
from product as p 
join sales as s 
on p.product_id = s.product_id 
group by p.category 
```

## 13.List products sold in more than 3 different regions.
```SQL
Select p.product_name , c. region, count(distinct s.order_id)
from product as p 
join sales as s
on p.product_id = s.product_id 
join customer as c
on s.customer_id = c.customer_id 
group by p.product_name, c.region
having count(distinct s.order_id) > 3
```

## 14.Identify customers who purchased the same product more than once.
```SQL
select c.customer_name , p.product_name,count(*) as purchase_count
from customer as c
join sales as s
on c.customer_id = s.customer_id 
join product as p 
on s.product_id = p.product_id 
group by c.customer_name, p.product_name
having count(*) > 1 
```

## 15.Find average profit margin per category.
```SQL
select p.category , round(avg(s.profit))
from product as p 
join sales as s
on p.product_id = s.product_id 
group by p.category
```

## 16.Show the top-selling product in each month.
```SQL
WITH monthly_sales as (
select to_char(order_date, 'YYYY-MM') as month,
product_id,SUM(quantity) as total_quantity
from sales
group by to_char(order_date, 'YYYY-MM'), product_id
),
ranked_sales as (
    select
        *,
        row_number() over (partition by month order by total_quantity desc) as rn
    from monthly_sales
)
select month, product_id, total_quantity
from ranked_sales
where rn = 1
order by month;
```

## 17.List customers who haven’t placed any orders this year.  

```SQL
With Customer_Names as(
Select c.customer_id , c.customer_name, s.order_id
from customer as c 
join sales as s 
on c.customer_id = s.customer_id 
where c.customer_id not in(
	Select distinct s.customer_id 
	from sales as s
	where extract(year from order_date) = extract (year from current_date))
)
select customer_id, customer_name, order_id
from customer_names
```

## 18.Calculate total discount given per region.
```SQL
select sum(discount) as total_discount, c.region 
from sales as s
join customer as c 
on s.customer_id = c.customer_id 
group by c.region
```

##19.Find customers with total order count above 10.
```SQL
with total_order_by_cust as(
Select c.customer_id , c.customer_name ,count(s.order_id) as total_order
from customer as c 
join sales as s 
on c.customer_id = s.customer_id 
group by c.customer_id , c.customer_name
) 
select customer_id , customer_name , total_order
from total_order_by_cust
where total_order > 10
order by total_order asc
```
## 20.Identify the month with the highest overall sales.
```SQL
select to_char(order_date, 'Month') as Month_name,
max(sales) as highest_sales
from sales 
group by order_date 
order by highest_sales desc
limit 1
```

