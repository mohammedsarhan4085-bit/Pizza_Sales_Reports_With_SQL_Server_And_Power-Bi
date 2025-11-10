# 🍕 Pizza Sales Analysis

This project delivers a complete analytical solution for pizza sales data using SQL Server views and Power BI dashboards. It includes performance metrics, sales trends, and top/bottom product insights to support business decisions.

---

## 📊 Overview

- **Date Range**: January 2015 – December 2015  
- **Total Revenue**: 817,860  
- **Total Orders**: 21,350  
- **Total Pizzas Sold**: 49,574  
- **Average Order Value**: 38.31  
- **Average Pizzas Per Order**: 2.32  

---

## 🧠 Data Model

The central table is `pizza_sales`, which includes:

- `order_id`, `order_date`, `pizza_name`, `pizza_category`, `pizza_size`, `quantity`, `total_price`

![Data Model](Images/Data_Modeling.PNG)

---

## 📈 Dashboard Insights

### 🔹 Home

![Home](Images/HOME.PNG)

- **Top Revenue Pizza**: Thai Chicken Pizza  
- **Top Quantity Pizza**: Classic Deluxe Pizza  
- **Worst Seller**: Brie Carre Pizza  

### 🔹Best And Worst Seller

![Best And Worst Seller](Images/Best_&_Worst_Seller.PNG)

- **Busiest Days**: Friday and Saturday  
- **Peak Months**: July to January  
- **Top Category**: Classic  
- **Top Size**: Large  

---

## 🧾 SQL Views (All-in-One Script)

```sql
USE pizza;
GO

-- Core Metrics
CREATE OR ALTER VIEW Total_Revenue AS
SELECT SUM(total_price) AS [Total Revenue] FROM pizza_sales;

CREATE OR ALTER VIEW AVG_Order_Value AS
SELECT SUM(total_price) / COUNT(DISTINCT order_id) AS [AVG_Order_Value] FROM pizza_sales;

CREATE OR ALTER VIEW Total_Pizzas_Sold AS
SELECT SUM(quantity) AS [Total_Pizzas_Sold] FROM pizza_sales;

CREATE OR ALTER VIEW Total_Orders AS
SELECT COUNT(DISTINCT order_id) AS [Total_Orders] FROM pizza_sales;

CREATE OR ALTER VIEW AVG_Pizzas_Per_Order AS
SELECT FORMAT(CAST(SUM(quantity) AS DECIMAL(10,2)) / CAST(COUNT(DISTINCT order_id) AS DECIMAL(10,2)), 'N2') AS [AVG_Pizzas_Per_Order] FROM pizza_sales;

-- Trends
CREATE OR ALTER VIEW Daily_Trend_For_Total_Orders AS
SELECT DATEPART(WEEKDAY, order_date) AS Day_Num,
       DATENAME(WEEKDAY, order_date) AS Order_day,
       COUNT(DISTINCT order_id) AS Total_orders
FROM pizza_sales
GROUP BY DATENAME(WEEKDAY, order_date), DATEPART(WEEKDAY, order_date);

CREATE OR ALTER VIEW monthly_trend_for_total_orders AS
SELECT DATEPART(MONTH, order_date) AS Month_Num,
       DATENAME(MONTH, order_date) AS Month_Name,
       COUNT(DISTINCT order_id) AS Total_orders
FROM pizza_sales
GROUP BY DATENAME(MONTH, order_date), DATEPART(MONTH, order_date);

-- Category & Size Breakdown
CREATE OR ALTER VIEW percent_Of_Sales_By_Pizza_Size AS
SELECT pizza_size,
       CAST(SUM(total_price) AS DECIMAL(10,2)) AS Total_Revenue,
       CAST(SUM(total_price) * 100.0 / (SELECT SUM(total_price) FROM pizza_sales) AS DECIMAL(10,2)) AS PCT
FROM pizza_sales
GROUP BY pizza_size;

CREATE OR ALTER VIEW percent_Of_Sales_By_Pizza_Category AS
SELECT pizza_category,
       CAST(SUM(total_price) AS DECIMAL(10,2)) AS Total_Revenue,
       CAST(SUM(total_price) * 100.0 / (SELECT SUM(total_price) FROM pizza_sales) AS DECIMAL(10,2)) AS PCT
FROM pizza_sales
GROUP BY pizza_category;

CREATE OR ALTER VIEW Total_Pizzas_Sold_by_Pizza_Category AS
SELECT pizza_category, SUM(quantity) AS Total_Quantity_Sold
FROM pizza_sales
GROUP BY pizza_category;

-- Top & Bottom Performers
CREATE OR ALTER VIEW Top_5_Pizzas_by_Revenue AS
SELECT TOP 5 pizza_name, SUM(total_price) AS Total_Revenue
FROM pizza_sales
GROUP BY pizza_name
ORDER BY Total_Revenue DESC;

CREATE OR ALTER VIEW Bottom_5_Pizzas_by_Revenue AS
SELECT TOP 5 pizza_name, SUM(total_price) AS Total_Revenue
FROM pizza_sales
GROUP BY pizza_name
ORDER BY Total_Revenue ASC;

CREATE OR ALTER VIEW Top_5_Pizzas_by_Quantity AS
SELECT TOP 5 pizza_name, SUM(quantity) AS Total_Pizzas_Sold
FROM pizza_sales
GROUP BY pizza_name
ORDER BY Total_Pizzas_Sold DESC;

CREATE OR ALTER VIEW bottom_5_Pizzas_by_Quantity AS
SELECT TOP 5 pizza_name, SUM(quantity) AS Total_Pizzas_Sold
FROM pizza_sales
GROUP BY pizza_name
ORDER BY Total_Pizzas_Sold ASC;

CREATE OR ALTER VIEW top_5_Pizzas_by_Total_Order AS
SELECT TOP 5 pizza_name, COUNT(DISTINCT order_id) AS Total_Orders
FROM pizza_sales
GROUP BY pizza_name
ORDER BY Total_Orders DESC;

CREATE OR ALTER VIEW bottom_5_Pizzas_by_Total_Order AS
SELECT TOP 5 pizza_name, COUNT(DISTINCT order_id) AS Total_Orders
FROM pizza_sales
GROUP BY pizza_name
ORDER BY Total_Orders ASC;
