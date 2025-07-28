# sql_retal_sales_p1
*SQL Retail Sales Analysis 
CREATE DATABASE sql_project_p1;

create TABLE retail_sales
(
             transactions_id INT primary key,
             sale_date DATE,
             sale_time TIME,	
             customer_id INT,
             gender	varchar(10),
             age INT,
             category VARCHAR(25),
             quantity INT,	
             price_per_unit	FLOAT,
             cogs FLOAT,
             total_sale FLOAT
);

SELECT * FROM retail_sales;

SELECT COUNT(*)  FROM retail_sales;
--2000 rows.

--Data cleaning
SELECT * FROM retail_sales
WHERE        transactions_id IS NULL
             OR
             sale_date IS NULL
			 OR
             sale_time IS NULL
			 OR
             customer_id IS NULL
			 OR
             gender	IS NULL
			 OR
             age IS NULL
			 OR
             category IS NULL
			 OR
             quantity IS NULL
			 OR
             price_per_unit	IS NULL
			 OR
             cogs IS NULL
			 OR
             total_sale IS NULL;
--Found 13 rows which has null values in either column.
 
DELETE FROM  retail_sales 
WHERE        transactions_id IS NULL
             OR
             sale_date IS NULL
			 OR
             sale_time IS NULL
			 OR
             customer_id IS NULL
			 OR
             gender	IS NULL
			 OR
             age IS NULL
			 OR
             category IS NULL
			 OR
             quantity IS NULL
			 OR
             price_per_unit	IS NULL
			 OR
             cogs IS NULL
			 OR
             total_sale IS NULL;



--Data Exploration--

--Q.How many sales do we have?
SELECT COUNT(total_sale) as total_no_of_sales
FROM retail_sales;

--Q.Find the total no. of customers.
SELECT COUNT(customer_id) as total_customer
FROM retail_sales;

## Q.Find the distinct categories.
SELECT Distinct (category) as total_category
FROM retail_sales;

--Data Analytics and Business Key Problems & Answers--
--Q1. Write a SQL query to retrieve all columns for the sales made on '2022-11-05'.
--Q2. Write a SQL query to retrieve all transactions where the category is 'clothing' and the quantity sold is more than 3 in the month of nov-2022.
--Q3. Write a SQL query to calculate the total sales(total_sale) for each category.
--Q4. Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
--Q5. Write a SQL query to find all transactions where the total sale greater than or equal to 1000 .
--Q6. Write a SQL query to find the total number of transactions (transactions_id) made by each gender in each category.
--Q7. Write a SQL query to calculate the average sale for each month, Find out best selling month in each year.
--Q8. Write a SQL query to find the top 5 customers based on the highest total sales.
--Q9. Write a SQL query to find the number of unique customers who purchased items from each category.
--Q10. Write a SQL query to create each shift and number of orders(Example Morning <=12,Afternoon Between 12 & 17,Evening >17).   
--Q11. How many sales were made by different age groups: Youth (<=25), Adults (26–50), and Seniors (>50)?
--Q12. What is the total revenue generated in the Morning, Afternoon, and Evening?(Morning <=12,Afternoon Between 12 & 17,Evening >17)
--Q13. Tag each transaction as “Profitable”, “Breakeven”, or “Loss” based on the difference between total_sale and cogs.
                 ------------------------------------------------------------

--Q1. Write a SQL query to retrieve all columns for the sales made on '2022-11-05'.
SELECT * FROM retail_sales
where sale_date = '2022-11-05'; 

--Q2. Write a SQL query to retrieve all transactions where the category is 'clothing' and the quantity sold is more than 3 in the month of nov-2022.
SELECT * 
FROM retail_sales
WHERE category = 'Clothing' 
      AND Quantity>3
	  AND sale_date BETWEEN '2022-11-01' AND '2022-11-30';

##Q3. Write a SQL query to calculate the total sales(total_sale) for each category.
'''
SELECT category, 
       SUM(total_sale) as Net_sale
FROM retail_sales
GROUP BY category;
--List down the count of categories in the previous question (descending order).
SELECT category, 
       SUM(total_sale) as Net_sale,
	   COUNT(*) as total_orders
FROM retail_sales
GROUP BY category
ORDER BY category DESC;
'''

--Q4. Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
SELECT AVG(age) as avg_age
FROM retail_sales
WHERE category = 'Beauty';

--Q5. Write a SQL query to find all transactions where the total sale is greater than or equal to 1000.
SELECT *
FROM retail_sales
WHERE total_sale>=1000;

--Q6. Write a SQL query to find the total number of transactions (transactions_id) made by each gender in each category.
SELECT category,
       gender,
       COUNT(transactions_id)	   
FROM retail_sales
GROUP BY gender,category
ORDER BY 1;

--Q7. Write a SQL query to calculate the average sale for each month, Find out best selling month in each year.
SELECT 
   AVG(total_sale) AS avg_sale,
   EXTRACT(year FROM sale_date) as year,
   EXTRACT(month FROM sale_date) as month
FROM retail_sales
GROUP BY YEAR,MONTH
ORDER BY 2,1 desc;  

--Q8. Write a SQL query to find the top 5 customers based on the highest total sales.   
SELECT customer_id,
       SUM(total_sale) as total_sale
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sale DESC
LIMIT 5;

--Q9. Write a SQL query to find the number of unique customers who purchased items from each category.
SELECT COUNT(DISTINCT customer_id),
       category
FROM retail_sales
GROUP BY category
ORDER BY 1;

--Q10.Write a SQL query to create each shift and number of orders(Example Morning <=12,Afternoon Between 12 & 17,Evening >17)       

SELECT COUNT(transactions_id),
    CASE
	   WHEN EXTRACT(Hour FROM sale_time)<12 THEN 'Morning'
	   WHEN EXTRACT(Hour FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
	   ELSE  'Evening'
	 END AS time_shift
FROM retail_sales
GROUP BY 2
ORDER BY 1;

--Q11. How many sales were made by different age groups: Youth (<=25), Adults (26–50), and Seniors (>50)?
SELECT COUNT(transactions_id),
     CASE 
	   WHEN age<=25 THEN 'Youth'
	   WHEN age BETWEEN 26 AND 50 THEN 'Adults'
	   ELSE 'Senior'
	 END AS Demographics
FROM retail_sales
GROUP BY 2
ORDER BY 1;

##Q12. What is the total revenue generated in the Morning, Afternoon, and Evening?(Morning <=12,Afternoon Between 12 & 17,Evening >17)
'''
SELECT 
  CASE 
    WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
    WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
    ELSE 'Evening'
  END AS time_shift,
  SUM(total_sale) AS total_revenue
FROM retail_sales
GROUP BY time_shift
ORDER BY 2;
'''

--Q13. Tag each transaction as “Profitable”, “Breakeven”, or “Loss” based on the difference between total_sale and cogs.
SELECT 
  transactions_id,
  total_sale,
  cogs,
  CASE 
    WHEN total_sale > cogs THEN 'Profitable'
    WHEN total_sale = cogs THEN 'Breakeven'
    ELSE 'Loss'
  END AS profit_status
FROM retail_sales;

                    -----All The Best----
                         --THANK-YOU--   





 
