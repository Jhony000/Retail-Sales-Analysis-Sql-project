# Retail-Sales-Analysis-SQL-Project

## Project Overview
##Project Title: Retail Sales Analysis
Level : Beginner
Database : Retails_

This projet is designed SQL skills and techniques used by data analysts to explore,clean and analyse retails sales data.The project involves settings up a retails database, performing exploratory data analysis(EDA)
and answering specific business questions through SQL queries. This pproject ideal for those who are starting their jouney in data analysis and want to build a solid foundation in SQL.

## Objectives
1.create and populate a retails sales database with the provided sales data
2.data cleaning : identify and remove any records with missing or null values.
3.exploratory data analysis : perform basic exploratory data analysis to understand the dataset.
4. business analysis: Use SQL to answer questions and drive insights from the sale data.

##dataset = Retails.csv
## database setup
## Database creation :the project is started by creating database name salesanalysis
## Table creation : A table name retails is created to store sales data. The table structure contain transaction_id,sale_date,sale_time,customer_id,gender,category,quantity,cogs,totalsales and datatypes also. 
## CREATE DATABASE salesanalysis;
USE salesanalysis;

## CREATE TABLE RETAILS (
    transaction_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(10),
    age INT,
    category VARCHAR(50),
    quantity INT,
    price_per_unit DECIMAL(10,2),
    cogs DECIMAL(10,2),
    total_sale DECIMAL(10,2)
);

SELECT COUNT(*) FROM retails;
SELECT * FROM retails;
## --- Data Cleaning
SELECT * FROM retails
where transaction_id is null
or sale_date is null
or sale_time is null 
or gender is null
or age is null
or category is null
or quantity is null
or cogs is null
or total_sale is null;
--
SET SQL_SAFE_UPDATES = 0;

## Delete null value 
DELETE FROM retails
where transaction_id is null
or sale_date is null
or sale_time is null 
or gender is null
or age is null
or category is null
or quantity is null
or cogs is null
or total_sale is null;

## -- Data Exploration 
-- how many sales we have?
SELECT COUNT(*) AS total_sale FROM retails;
-- how many unique customer we have?
SELECT COUNT(distinct customer_id) AS total_sale FROM retails;
-- how many category we have?
SELECT distinct category From retails;

## -- Data analyze& business key problem amd answer

-- 1.write a sql query to retrieve all columns for sales made on "2022-11-05".
 SELECT * FROM retails where sale_date =  "2022-11-05";

 ## -- 2. write a sql query to retrieve all transaction where the category is 'clothing' and the quantity sold is more than 10 in the month of NOV-2022 
SELECT *
FROM retails
WHERE category = 'Clothing'
  AND DATE_FORMAT(sale_date, '%Y-%m') = '2022-11'
  AND quantity >= 3;

 ##  -- 3. Write a SQL query to calculate the total_sales (total_sale) for each category.
  Select 
       category,
       sum(total_sale) as net_sale,
       count(*) as total 
from retails 
group by 1;

## -- 4. Write a Sql query to find the average age of customer who purchased iteams from beauty category.
SELECT 
     AVG(age) as avg_age from retails 
where category = 'Beauty';

## -- 5. write a sql query to find all the transaction where the total sales is greater than 1000
SELECT 
    count(*) from retails
 where total_sale > 1000;

## -- 6. write a sql query to find total number of trancation(transcation_id) made by each gender by each category.
Select 
     gender,
     category,
     count(*) as c 
from retails 
group by gender,category;

## -- 7. write a sql query to calculate average sale for each month.find out best selling months in  each year.
select 
    year,
    month,
    avg_sale
from
(
select 
    extract(year from sale_date) as year,
    extract(month from sale_date) as month,
    avg(total_sale) as avg_sale,
    RANK() OVER(partition by EXTRACT(YEAR FROM sale_date) order by avg(total_sale) desc) AS rank
from retails 
group by 1,2
) as t1
where rank =1;
--  -- order by 1,2,3 DESC;

## -- 8.Write a SQL query to find the top 5 customers based on the highest total sales?
 select 
      customer_id,
      sum(total_sale) as total_sales 
from retails 
group by 1 
order by 2 desc 
limit 5;

 ## -- 9.Write a SQL query to find the number of unique customers who purchased  items from each category?
 select 
     count(distinct customer_id),
     category from retails 
group by category ;
 
 ## -- 10.Write a sql query to create each shift and number of orders(example morning <=12, afternoon between 12&17,evening >17)
 with hourly_sale
 as
 (
 select *,
      case
          when extract(hour from sale_time)< 12 then 'morning'
          when extract(hour from sale_time) between 12 and 17 then 'afternoon'
          else 'evening'
       end as shift 
from retails
)
select
      shift,
      count(*) as total_orders
from hourly_sale
group by shift;
