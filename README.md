# DW_lab_6730204908
lab1 Data Warehouse 

Paste the following into README.md file

## Introducing OLAP for Northwind OLTP Database
**What's the current setup or architecture?**

- Northwind traders are companies that buy and sell special foods worldwide.
- This is a practice database made by Microsoft to showcase its product features and for learning purposes.
- The current setup combines on-site and older systems.
- They use MySQL for their main daily sales transactions.
- MySQL is also used for creating and running reports, but it's not efficient because analytical queries slow down the transaction system.

### Identifying Business Requirements
Throughout the interview process with the business and stakeholders, the following business processses were identified:
- **Sales Overview:**
Overall sales reports to understand better, what is being sold to our customers, what sells the most, where and what sells the least, the goal is to have a general overview of how the business is going.

This means the business is looking forward to getting insights on sales overview.

### Identifying required tables from ERD
<img src="./readme_images/northwind-oltp-erd.png">
From the above ERD diagram of the OLTP transactional system, we identify the following required tables that will enable us to meet the business requirements:

<br>
<li>Customers - Customers who buy items from Northwind</li>
<li>Employees - Those who work for Northwind</li>
<li>Orders - Sales Order transactions taking place between the customers & Northwind</li>
<li>Order Details - Order Details for the Orders placed by customer</li>
<li>Inventory Transaction - Transaction details of each inventory</li>
<li>Products - Current Northwind products that customers can purchase</li>
<li>Shippers - Shipped orders from Northwind to customers</li>
<li>Suppliers - Supplies Northwind with required items</li>
<li>Invoices - Invoice created for each order</li>

### **Staging Layer**
In the staging layer, we have the following tables:
- customers: load customers from datasets/customer.csv and insert ingestion timestamp.
- employees: load employees from datasets/employees.csv and insert ingestion timestamp.
- orders: load orders from datasets/orders.csv and insert ingestion timestamp.
- order_details: load order_details from datasets/order_details.csv and insert ingestion timestamp.
- inventory_transactions: load inventory_transactions from datasets/inventory_transactions.csv and insert ingestion timestamp.
- products: load products from datasets/products.csv and filter out rows where supplier_ids contains multiple semicolon-delimited values and insert ingestion timestamp.
- shippers: load shippers from datasets/shippers.csv and insert ingestion timestamp.
- suppliers: load suppliers from datasets/suppliers.csv and insert ingestion timestamp.
- invoices: load invoices from datasets/invoices.csv and insert ingestion timestamp.

### ***Datawarhouse Layer***
<img src="./readme_images/logical-model.png">

- dim_products: load data from stg_products, join supplier information from stg_suppliers, rename id to product_id, deduplicate records by product_id using row_number(), and insert insertion timestamp.
- dim_date: create the date dimension from 2014-01-01 to 2050-01-01 using GENERATE_DATE_ARRAY, create id as the formatted date key in YYYY-MM-DD, derive full_date, year, year_week, year_day, fiscal_year, fiscal_qtr, month, month_name, week_day, day_name, and day_is_weekday, and include all rows for each calendar date.
- fact_sales: combine order and order detail data from stg_orders and stg_order_details, join on order_id, include sales facts such as quantity, unit_price, discount, dates, and insertion timestamp, and deduplicate records by customer_id, employee_id, order_id, product_id, shipper_id, purchase_order_id, and order_date using row_number().
