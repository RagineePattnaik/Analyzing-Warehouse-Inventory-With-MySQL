# Analyzing-Warehouse-Inventory-With-MySQL
# Mint Classics Warehouse Inventory Analysis

## *Table of Contents*

1. [Project Scenario](#project-scenario)
2. [Project Objectives](#project-objectives)
3. [Data Sources](#data-sources)
4. [Project Tools](#project-tools)
5. [Data cleaning](#data-cleaning)
6. [Exploratory Data Analysis](#exploratory-data-analysis)
7. [Data Analysis](#data-analysis)
8. [Findings](#findings)
9. [Recommendation](#recommendation)
10. [Limitations](#limitations)
11. [References](#references)

### Project Scenario

Mint Classics Company, a retailer of classic model cars and other vehicles, is looking at closing one of their storage facilities. To support a data-based business decision, they are looking for suggestions and recommendations for reorganising or reducing inventory, while still maintaining timely service to their customers. For example, they would like to be able to ship a product to a customer within 24 hours of the order being placed. 

As a data analyst, I have been asked to use MySQL Workbench to familiarise yourself with the general business by examining the current data. You will be provided with a data model and sample data tables to review. You will then need to isolate and identify those parts of the data that could be useful in deciding how to reduce inventory. 

### Project Objectives

- Import an existing database using MySQL Workbench.
- Get familliar with the business and its data by reviewing a relational model diagram and exploring tables of 
  data in MySQL Workbench.
- Analyse inventory data using SQL queries in MySQL Workbench that retrieve data from a multiple-table 
  relational database using SQL commands such as: select, order by, where, group by, and having.
- Develop recommendations and suggestions for solving a business need/problem based on data analysis.
- Support the recommendations and suggestions for inventory reduction in the form of scripted queries.
- Share the findings.

### Data Sources
- Inventory Data: The primary dataset used for this project is the "mintclassicsDB.sql" file. 
- For reference and use in analysis, the Extended Entity-Relationship "Mint_Classics_ERD.png" diagram that 
  models the structure of the Mint Classics database was provided

### Project Tools
- MySQL workbench- Data manipulation, Data Analysis
- EXEL- Data Visualization
- Google - Research
### Data cleaning
in the initial data cleaning and data preparation phase I performed the following tasks:
- Data loading and inspection
- Data formating
- SQL commands such as: DISTINCT to remove duplicates

### Exploratory Data Analysis
The EDA involves exploring the data to answer key questions such as:

1) Where are items stored and if they were rearranged, could a warehouse be eliminated?
2) what is the ovarall sales figures for each warehouse. 
4) How are inventory numbers related to sales figures? Do the inventory counts seem appropriate for each item?
5) Are we storing items that are not moving?  
### Data Analysis
- Use SQL queries for Data Analysis
- Queries important factors that may influence inventory reorganization/reduction

Query the data from each table of the provided database to see what kind of data they have.
```sql
SELECT* FROM customers;
SELECT* FROM employees;
SELECT* FROM offices;
SELECT* FROM orders;
SELECT* FROM payments;
SELECT* FROM productLines;
SELECT* FROM  products;
SELECT* FROM warehouses;
```

- After querying the data from each table, choose the data you need from the tables and create a new table that contains the combined data.
- I am going to call the new table “Warehouse_Inventory”
- Change the naming conventions into the ones that are readable and understandable. 

```sql
CREATE TABLE  Warehouse_Inventory AS SELECT 
products.productLine AS Vehicle_Type, products.productScale AS Vehicle_Scale,
products.quantityInStock AS Vehicles_In_Stock , products.buyPrice AS Buy_Price,
products.MSRP AS Suggested_Retail_Price,
warehouses.warehouseName AS Warehouse_Name,warehouses.warehousePctCap AS  Warehouse_Storage_Capacity,
orderdetails.orderNumber AS Num_Of_Orders ,orderdetails.quantityOrdered AS Vehicle_Ordered, 
orderdetails.priceEach AS Price_Per_Vehicle,
orders.status As Shipping_Status, orders.customerNumber AS Customer_Number
FROM products
INNER JOIN  warehouses  ON products.warehouseCode = warehouses.warehouseCode
INNER JOIN orderdetails  ON products.productCode = orderdetails.productCode
INNER JOIN orders  ON  orderdetails.orderNumber = orders.orderNumber; 
```
- Query the new Warehouse_Inventory  table to see the data
```sql
   SELECT* FROM Warehouse_Inventory;
```
- Find out the vehicle type stored in each warehouse.
```sql
    SELECT DISTINCT Vehicle_Type, Warehouse_Name FROM Warehouse_Inventory;
```
- Find out which warehouse has the most occupied storage capacity
```sql
SELECT DISTINCT Warehouse_Name, Warehouse_Storage_Capacity
FROM Warehouse_Inventory 
ORDER BY    Warehouse_Storage_Capacity DESC;
```
- Find the sum of all the vehicles in stock for each warehouse. 
```sql
SELECT DISTINCT Warehouse_Name, SUM(Vehicles_In_Stock) AS Vehicles_Per_Warehouse 
FROM Warehouse_Inventory 
GROUP BY Warehouse_Name ;
```
- Find the sum of all the orders per warehouse
```sql
SELECT DISTINCT Warehouse_Name, SUM(Num_Of_Orders) AS Num_Orders
FROM Warehouse_Inventory 
GROUP BY Warehouse_Name;
```
- Find out which warehouse has the most shippings
```sql
SELECT DISTINCT Warehouse_Name, COUNT(Shipping_Status) Orders_Shipped
FROM Warehouse_Inventory  
WHERE Shipping_Status = "Shipped"
GROUP BY  Warehouse_Name;
```

- Find out which warehouse has the shipping orders cancelled
```sql
 SELECT DISTINCT Warehouse_Name, COUNT(Shipping_Status) AS Orders_Cancelled
 FROM Warehouse_Inventory 
 WHERE Shipping_Status = "Cancelled"
 GROUP BY  Warehouse_Name
 ORDER BY Orders_Cancelled DESC; 
```

- Find out which warehouse makes the most profit, considering the buy price and sell price
```sql
SELECT  Warehouse_Name, SUM(Buy_Price) AS BUY_PRICE, SUM(Price_Per_Vehicle) AS SELL_PRICE,  
(( SUM(Price_Per_Vehicle) -  SUM(Buy_Price)) /  SUM(Buy_Price)) * 100 AS Profit_Percentage
FROM Warehouse_Inventory 
GROUP BY  Warehouse_Name; 
```

### Findings
As per my analysis of the data this are my findings:

1) None of the warehouses has 100% of its storage occupied, the south warehouse has the largest storage capacity with 75% of the storage occupied because it stores large vehicles like buses, trains and trucks.

![Storage Capacity](https://github.com/Khanyisa06/Warehouse-Inventory-Analysis/assets/106344554/bf20adf3-be57-4d1f-9ab9-37553b53671a)

2) The warehouse that has the lowest number of customers is the South warehouse. Having the least indicate lower demand in the South region.
![Customers](https://github.com/Khanyisa06/Warehouse-Inventory-Analysis/assets/106344554/0e5619ff-649c-4004-9e76-0ce1ddd6662b)

3) Compared to North, East and West warehouses,the lowest customer orders comes from the South warehouse.

![Orders](https://github.com/Khanyisa06/Warehouse-Inventory-Analysis/assets/106344554/e13a21d2-ed6a-4eee-891b-fe2886c808fd)

4) The warehouse that has a high cancellation rate of orders is the South warehouse, this indicate issues with customer satisfaction in this region.

![Orders cancelled](https://github.com/Khanyisa06/Warehouse-Inventory-Analysis/assets/106344554/01e77b8f-5885-4b0e-bd8f-ab7992d5388b)

5) The South warehouse warehouse has fewer shipments is compared to North, East and West warehouses. Fewer shipments could be indicative of lower customer demand or a lack of popularity for products.

![Orders Shipped](https://github.com/Khanyisa06/Warehouse-Inventory-Analysis/assets/106344554/4dae42d7-4feb-4d80-98d2-d0c0e3940468)

6) The South warehouse produces lower profit compared to the others considering the buy and sell price.  .
![Profit](https://github.com/Khanyisa06/Warehouse-Inventory-Analysis/assets/106344554/ffebe372-593c-479b-9e37-5301d21f360d)



### Recommendation
Based on my analysis I recommend the following actions:

1) Consider shutting down the South warehouse.
  - Reasons tosupport this decision:
    - The warehouse is underperforming in terms of profit. It may not be contributing significantly to the company's revenue.
    - Shutting down the warehouse could lead to cost savings in terms of rent, labor, and other operational expenses. The resources can then be allocated more effectively elsewhere.
    - Products from the warehousecan be redistributed to other locations where they might have better sales. 
    
3) Evaluate the inventory in all the warehouses, identify slow-moving or obsolete items, and consider reducing or discontinuing them.
4) Consider reallocating or consolidating storage space based on the actual demand. Ensure that the South storage is not over-allocated in terms of occupation capacity.
5) Evaluate the storage layout and organization in each warehouse. Ensure that items are easily accessible, and storage space is used efficiently.

### Limitations
I have removed all the tables and colmns from the dataset that I didnt need because they would have affected the accuracy of my analysis and findings

### References




















