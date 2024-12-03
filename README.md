# Instacart-Data-Analysis-Project-using-python-and-sql
## Description:
Performed data analysis on Instacart order records using SQL and Python to understand customer behavior and product trends. Processed and transformed data to calculate metrics such as reorder rates, product demand, and purchase patterns. Identified factors driving repeat orders and peak shopping periods. Provided insights to enhance inventory planning and targeted marketing efforts.

### About Instacart: 
Instacart is an American company that provides a platform for online grocery delivery and pick-up services. It partners with local grocery stores and retailers to enable customers to shop for groceries and household items through the Instacart app or website. A personal shopper picks and delivers the orders to the customer's doorstep or prepares them for pick-up.

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
1. Retrieve details for each order, including the purchased products along with their corresponding department and aisle information.
   To perform this action, you would execute the following SQL steps:

* Create a temporary table by joining the orders, order_products, aisle, department and products tables.
* Select relevant columns from each table to include information about the orders, purchased products, and their respective departments and aisles.


*Execution code block*
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
1. Creating the Temporary Table:
```CREATE TEMPORARY TABLE order_details AS```: This creates a temporary table named order_details which will store the results of the query.
```SELECT```: Retrieves specific columns like order_id, order_number, user_id, etc., from the relevant tables.
```JOIN```: The query uses INNER JOINs to combine data from:
orders table (r),
order_products table (o),
products table (p),
aisles table (a),
departments table (d),
```ORDER BY r.order_number ASC```: Sorts the results by order_number in ascending order.

2. Verifying the Temporary Table:
```SELECT * FROM order_details; ```: This command retrieves all the data from the temporary table order_details to verify that the table was created and populated correctly.

*Query screenshot LIMIT 10:*

