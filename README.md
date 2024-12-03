# Instacart-Data-Analysis-Project-using-python-and-sql
## Description:
In this project, I developed a comprehensive analytical framework to gain actionable insights into product sales and customer behaviors. By aggregating data from multiple sources, including product, department, and aisle-level details, I created a series of temporary tables to calculate key metrics such as total orders, reorder frequency, product add-to-cart behaviors, and purchase trends.

Using SQL, I joined these insights to generate a unified view, allowing for an in-depth analysis of product performance, customer preferences, and purchase patterns across different departments and aisles. The results were summarized in a final report that provided a clear understanding of weekday vs. weekend purchases, unique product purchases, and average order times. This analysis supports better inventory management, targeted promotions, and enhanced product placement strategies for improved sales performance.

### About Instacart: 
*Instacart is an American company that provides a platform for online grocery delivery and pick-up services. It partners with local grocery stores and retailers to enable customers to shop for groceries and household items through the Instacart app or website. A personal shopper picks and delivers the orders to the customer's doorstep or prepares them for pick-up.*

### Project Data Model
![Diagram](https://github.com/Joshh90/Instacart-Data-Analysis-Project-using-python-and-sql/blob/main/Instacart%20project%20datamodel.drawio.png)
### About dataset
1. **aisles**: Contains details about various product categories (aisles).
2. **departments**: Includes information about different store departments.
3. **order_product**: Contains product details dataset of customer orders.
4. **orders**: Provides data on individual orders and associated customers.
5. **products**: Includes information about products, along with their aisle and department IDs.

[Dataset link](https://drive.google.com/drive/folders/1obgx7Z6zGAxSIt-6_sVEDkpUotGkWAB2?usp=drive_link)


### Services used for the project
1. **Jupyter notebook**:
Jupyter Notebook was used for writing and executing the SQL code. It serves as an interactive environment to query databases, explore results, and document your process.
3. **Python**:Python acts as a flexible and dynamic programming tool to interact with databases and process data. Python acts as a bridge to enhance SQL querying with advanced data manipulation, automation, and visualization capabilities. It allows seamless integration of SQL for querying databases with Python libraries for further analysis and processing.
4. **Sql**:(Structured Query Language) is a standard programming language designed to manage and manipulate data stored in relational database systems. It allows users to create, read, update, and delete (CRUD) data in databases efficiently.
5. **Postgresql**: Is a powerful, open-source, object-relational database management system (RDBMS) that emphasizes extensibility, reliability, and compliance with SQL standards. It is widely used for handling structured data and supports both small-scale and large-scale applications.

### Key Business Questions to Address:
1. *How can we retrieve details for each order, including the purchased products along with their corresponding department and aisle information?*
   To perform this action, you would execute the following SQL steps:

* Create a temporary table by joining the orders, order_products, aisle, department and products tables.
* Select relevant columns from each table to include information about the orders, purchased products, and their respective departments and aisles.


*Execution code block:*
```
CREATE TEMPORARY TABLE order_details AS

SELECT
r.order_id,
r.order_number,
r.user_id,
r.order_dow,
r.order_hour_of_day,
r.days_since_prior_order,
o.product_id,
p.product_name,
p.aisle_id,
a.aisle,
p.department_id,
d.department

FROM
    orders r
JOIN
    order_products o ON r.order_id = o.order_id
JOIN
	products p ON o.product_id = p.product_id
JOIN
	aisles a ON p.aisle_id = a.aisle_id
JOIN
	departments d ON p.department_id = d.department_id
ORDER BY 
	r.order_number ASC; ---we arrange the query based on order number in ascending order

--CHECK:
SELECT * FROM order_details;


```
 *Here's a quick breakdown of the query*:
* Creating the Temporary Table:
```CREATE TEMPORARY TABLE order_details AS```: This creates a temporary table named order_details which will store the results of the query.
```SELECT```: Retrieves specific columns like order_id, order_number, user_id, etc., from the relevant tables.
```JOIN```: The query uses INNER JOINs to combine data from:
orders table (r),
order_products table (o),
products table (p),
aisles table (a),
departments table (d),
```ORDER BY r.order_number ASC```: Sorts the results by order_number in ascending order.

* Verifying the Temporary Table:
```SELECT * FROM order_details; ```: This command retrieves all the data from the temporary table order_details to verify that the table was created and populated correctly.

*Query output screenshot LIMIT 10:*
[Dataset link 1](https://github.com/Joshh90/Instacart-Data-Analysis-Project-using-python-and-sql/blob/main/Ist%20screenshot.jpg)
[Dataset link 2](https://github.com/Joshh90/Instacart-Data-Analysis-Project-using-python-and-sql/blob/main/2nd%20screenshot.jpg)

2. *How can we create a temporary table that groups orders by product to determine the total number of purchases, the total number of reorders, and the average number of times each product was added to a cart?*
To perform this action, you would execute the following SQL steps:

* Create a Temporary Table: Use the CREATE TEMPORARY TABLE statement to define a new temporary table Order_information that will store the results of the query.

* Join Tables: Join the orders, order_products, and products tables to combine order, product, and product details based on order_id and product_id.

* Aggregate Data: Use aggregate functions:

use *count( )*  function to calculate the total number of orders per product.
SUM(CASE WHEN op.reordered = 1 THEN 1 ELSE 0 END) to calculate the total reorders per product.
AVG(op.add_to_cart_order) to calculate the average times each product was added to a cart.
* Group the Data: Group the results by product_name and product_id to calculate the aggregates per product.

* Verify the Data: Use SELECT * FROM Order_information to view and verify the data stored in the temporary table.

  *Execution code block:*
```
-- Step 1: Create a Temporary Table for Order Information
CREATE TEMPORARY TABLE Order_information AS
SELECT 
    p.product_id, 
    p.product_name,
    COUNT(*) AS total_orders, -- Total number of orders for each product
    SUM(CASE WHEN op.reordered = 1 THEN 1 ELSE 0 END) AS Total_no_of_reordered, -- Total number of reorders (1 for reorder)
    AVG(op.add_to_cart_order) AS Avg_no_of_times_of_add_to_cart -- Average times of adding to cart
FROM 
    orders r
JOIN
    order_products op ON r.order_id = op.order_id
JOIN
    products p ON op.product_id = p.product_id
GROUP BY
    p.product_name, p.product_id
ORDER BY
    total_orders DESC; -- Optional: Sort by total orders in descending order

-- Step 2: Verify the Data
SELECT * FROM Order_information;
```
*Query output screenshot LIMIT 10:*
[Dataset link 3](https://github.com/Joshh90/Instacart-Data-Analysis-Project-using-python-and-sql/blob/main/3rd%20screenshot.jpg)

3.  *How can we create a temporary table to group orders by department and determine the total number of products purchased, the total number of unique products purchased, the breakdown of purchases on weekdays vs. weekends, and the average time of day products are ordered?*
```
SELECT department_id, department,
COUNT(*) AS total_product_purchased,
COUNT(DISTINCT product_id) AS total_unique_product_purchased,
COUNT(CASE WHEN order_dow < 6 THEN 1 ELSE NULL END) AS total_weekday_purchases,
COUNT(CASE WHEN order_dow >= 6 THEN 1 ELSE NULL END) AS total_weekend_purchases,
AVG(order_hour_of_day) AS avg_order_time

```

*Here's a quick breakdown of the query*:
* Create a Temporary Table: Use the ```CREATE TEMPORARY TABLE``` command to define a new table department_order_summary for storing the aggregated results.

* Perform Aggregations: Select department_id and department, then calculate:

```
Total products purchased (COUNT(*)).
Unique products purchased (COUNT(DISTINCT product_id)).
Weekday and weekend purchases using conditional CASE statements.
Average order time (AVG(order_hour_of_day)).
* Group by Department: Use GROUP BY department_id, department to aggregate the results for each department.
```

* Verify Results: Run ```SELECT * FROM department_order_summary``` to view and validate the generated summary.

  
*Query output screenshot LIMIT 10:*
[Dataset link 4](https://github.com/Joshh90/Instacart-Data-Analysis-Project-using-python-and-sql/blob/main/4th%20screenshot.jpg)

4. *What are the top 10 most popular aisles based on the total number of products purchased, and how many unique products were purchased from each aisle?*

*Execution code block:*

```  
CREATE TEMPORARY TABLE aisle_order_summary AS
SELECT 
    aisle_id, 
    aisle,
    DENSE_RANK() OVER (ORDER BY COUNT(*) DESC) AS rank, -- Rank aisles by total products purchased
    COUNT(*) AS total_products_purchased, -- Total number of products purchased from the aisle
    COUNT(DISTINCT product_id) AS total_unique_product_purchased -- Total unique products purchased from the aisle
FROM 
    order_details
GROUP BY 
    aisle_id, aisle -- Group by aisle information
ORDER BY 
    total_products_purchased DESC; -- Order aisles by total products purchased

-- CHECK
SELECT * FROM aisle_order_summary WHERE rank <= 10 LIMIT 10; -- Retrieve only the top 10 aisles

```
*Here's a quick breakdown of the query*:
* Create the Temporary Table: Run the query to generate aisle_order_summary with rankings and purchase data.
* Filter Top 10 Aisles: Use ```SELECT * FROM aisle_order_summary WHERE rank <= 10``` to retrieve only the top 10 aisles.
* Verify Results: Check the data to ensure correctness and rankings are as expected.

*Query output screenshot LIMIT 10:*
[Dataset link 5](https://github.com/Joshh90/Instacart-Data-Analysis-Project-using-python-and-sql/blob/main/5th%20screenshot.jpg)

5. *How can we create a final table that consolidates product and department-level insights, including product details, purchase metrics, reorder trends, and aisle and department-level aggregated statistics such as weekday/weekend purchases and average order times?*

*Execution code block:*
```
  WITH product_sales_insights AS (
    SELECT 
        oi.product_id,
        oi.product_name,
        ds.department_id,
        ds.department,
        ao.aisle_id,
        ao.aisle,
        oi.total_orders,
        oi.total_no_of_reordered,
        oi.avg_no_of_times_of_add_to_cart,
        ao.total_products_purchased,
        ao.total_unique_product_purchased,
        ds.total_weekday_purchases,
        ds.total_weekend_purchases,
        ds.avg_order_time
    FROM order_details o
    LEFT JOIN order_information oi ON o.product_id = oi.product_id
    LEFT JOIN department_order_summary ds ON o.department_id = ds.department_id
    LEFT JOIN aisle_order_summary ao ON o.aisle_id = ao.aisle_id
)
SELECT * 
FROM product_sales_insights LIMIT 10;
```
*Here's a quick breakdown of the query*:
* Product-level metrics like purchase frequency and reorder behavior.
* Department and aisle-level trends, including total and unique purchases.
* Insights into when and how often products are added to carts and purchased.

*Query output screenshot LIMIT 10:*
[Dataset link 6](https://github.com/Joshh90/Instacart-Data-Analysis-Project-using-python-and-sql/blob/main/6th%20screenshot.jpg)
[Dataset link 7](https://github.com/Joshh90/Instacart-Data-Analysis-Project-using-python-and-sql/blob/main/7th%20screenshot.jpg)

## Summary:
In this project, I analyzed product sales and customer behavior by aggregating data from product, department, and aisle levels. Using SQL, I calculated key metrics like total orders, reorders, and add-to-cart behavior, while also analyzing weekday vs. weekend purchase trends. The final insights provided a unified view of product performance, helping optimize inventory and marketing strategies across departments and aisles.



  



