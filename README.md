 --sql Retail sales project 1 
 create database retail_sales;

 -- create table 
 create table dbo.Retail_Sales_Analysis
			(
				transactions_id	int Primary key,
				sale_date date,
				sale_time	time, 
				customer_id	int,
				gender	varchar(15),
				age	int,
				category varchar(15),	
				quantiy	int,
				price_per_unit	float, 
				cogs	float,
				total_sale float 
			);

select *
from  dbo.Retail_Sales_Analysis;

-- see all the table
select *
from  dbo.Retail_Sales_Analysis;

-- count to see how many records 
select count(*)
from  dbo.Retail_Sales_Analysis;

-- find the null in transactions_id 
select *
from  dbo.Retail_Sales_Analysis
where transactions_id is null 

-- find the null in sale_Date
select *
from dbo.Retail_Sales_Analysis
where sale_date is null


-- find the null in sale_time
select * 
from dbo.Retail_Sales_Analysis
where sale_time is null

--  data cleaning  
select *
from dbo.Retail_Sales_Analysis
where 
	transactions_id is null
	or 
	sale_date is null
	or
	sale_time is null
	or
	gender is null
	or
	category is null
	or
	quantiy is null
	or 
	price_per_unit is null
	or 
	cogs is null
	or 
	total_sale is null;

-- data exploration
-- how many sales we have?
select count(*) as total_Sales 
from dbo.Retail_Sales_Analysis

-- how many  unique customers we have?
select count(distinct customer_id) as total_sales
from dbo.Retail_Sales_Analysis

-- how many category we have?
select distinct  category 
from dbo.Retail_Sales_Analysis

-- busniess key problems and answers 

--1 write a SQL query to retrieve all columns for sales made on '2022-11-05'
select *
from dbo.Retail_Sales_Analysis
where sale_date = '2022-11-05';


-- 2 write a SQL query to retrieve all transactions where the category is 'clothing'and the quantity sold is more than 10 in the month of nov 2022
select 
	category,
	sum(quantiy)
from dbo.Retail_Sales_Analysis
where category = 'Clothing'
	and 
	quantiy >= 4

group by category 


-- 3 write a sql query to calculate the total sale (total_Sale) for each category.
select 
	category, 
	sum(total_sale) as net_Sale,
	count(*) as total_orders 
from dbo.Retail_Sales_Analysis
group by category


-- 4 write a sql query to find the average age of customers who purchased items from the 'beauty' category.
select 
	round(avg(age),2) as avg_Age
from dbo.Retail_Sales_Analysis
where category = 'beauty'

--5 write a sql query to find all the transactions where the total sale is greater than 1000
select *
from dbo.Retail_Sales_Analysis
where total_sale > 1000


-- 6 write a sql to find the total number of transaction (transaction_id) made by each gender in each category.
select
	category,
	gender,
	count(*) as total_transaction
from dbo.Retail_Sales_Analysis
group by category, gender


-- 7 write a sql query to calcualte the average sale for ech month. find out the best selling month in each year.

-- using top 1 with ties method is most efficient for large datasets with automatic tie handling 
select top 1 with ties
	year(sale_date) as sale_year,
	month(sale_date) as sale_month,
	sum(total_sale) as total_sales
from dbo.Retail_Sales_Analysis
group by year(sale_date),
month(sale_date)
order by rank() over(partition by year(sale_date) order by sum(total_sale) desc);


	
--- extract top 5 
select top 5 * from dbo.Retail_Sales_Analysis;


-- 8 write a sql query to find the top 5 customers based on the highest total sales
select top 5
	customer_id,
	sum(total_sale) as total_sales
from dbo.Retail_Sales_Analysis
group by customer_id
order by  customer_id desc

-- 9 write a sql query to find the number of unique customers who purchased item from each category
select
	category,
	count(distinct customer_id) as unique_customer
from dbo.Retail_Sales_Analysis
group by category
order by unique_customer desc;


-- 10 write sql query to create each shift and number of orders (example morning <=12, afternoon between 12 and 17 , evening 17)

with hourly_sale as(
select *,
	case 
		when  Datepart (hour , sale_time) <= 12 then 'Morning'
		when  Datepart (hour , sale_time) between 12 and 17 then 'Afternoon'
		else 'Evening'
	end as shift
from dbo.Retail_Sales_Analysis
)
select 
	shift,
	count(*) as total_orders
from  hourly_sale
group by shift;


-- 11 what is total revenue generated from sales?
select sum(total_sale) as total_sales
from dbo.Retail_Sales_Analysis


--12 what is total number of transactions?
select count(transactions_id) as total_trans
from dbo.Retail_Sales_Analysis

--13 what is average sales value per transaction?
select avg(total_sale) as avg_sale
from dbo.Retail_Sales_Analysis

--14 how many unique customers made purchase?
select count(distinct customer_id)  as unique_customer
from dbo.Retail_Sales_Analysis

-- 15 whcih category has the highest num of unique customer
select category, count(distinct  customer_id)  as unique_customer
from dbo.Retail_Sales_Analysis
group by category
order by unique_customer desc;

-- 16 what is age distribution of customer who purchased items
select age, count(customer_id) as customer_count
from dbo.Retail_Sales_Analysis
group by age
order by age

 -- 17 which customer have  most transactions
 select top 5 customer_id, count(transactions_id) as transaction_count
 from dbo.Retail_Sales_Analysis
 group by customer_id
 order by transaction_count desc 

 -- 18 what is revenye contribution of each category
 select category,
	sum(total_sale) as total_revenue, 
	(sum(total_sale) * 100.0) / (select sum(total_sale) from dbo.Retail_Sales_Analysis) as revenue_percentage
from dbo.Retail_Sales_Analysis
group by category
order by revenue_percentage desc;

-- 19 what is total num of transaction per day?
select sale_date, count(transactions_id) as total_trans
from dbo.Retail_Sales_Analysis
group by sale_date
order by sale_date;

-- 20 what is most frequently purchased product?
select category, count(*) as purchased_count
from dbo.Retail_Sales_Analysis
group by category
order by purchased_count desc 

-- 21 what is avg order value
select sum(total_sale)/ count(distinct transactions_id) as avg_order_value
from dbo.Retail_Sales_Analysis

--22 total revenue per customer
select customer_id, sum(total_sale) as total_spent
from dbo.Retail_Sales_Analysis
group by customer_id
order by total_spent desc;

-- 23 total revenue per category
select category, sum(total_sale) as total_Revenue
from dbo.Retail_Sales_Analysis
group by category
order by total_revenue desc;

--- end of the project -----
