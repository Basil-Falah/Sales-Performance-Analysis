# Sales Performance Analysis | SQL + Power BI

## Overview
Analyzed AdventureWorks sales data using SQL for data extraction 
and Power BI for visualization, to identify key revenue drivers, 
top-performing regions, and profitability trends.

## Tools
SQL Server (SSMS) | Power BI Desktop | DAX

## Key Insights
- Total sales reached 109.85M with a gross profit margin of 8.53%
- Southwest is the top-performing territory (24M), followed by 
  Canada and Northwest (16M each)
- Bikes dominate total revenue at 86.17%, while Components, 
  Clothing, and Accessories combined make up less than 14%
- Helmets lead all subcategories in both sales and gross profit, 
  with Mountain-200 as the best-selling individual product

## Dashboard
![Sales Performance Dashboard](https://github.com/user-attachments/assets/e26c3f1a-cc85-46d9-ba3b-2554fad059db)
## SQL Queries
```sql


-- 1. Total Sales By Year and Month 
select year(OrderDate) as YR,
	   month(OrderDate) as MO,
	   FORMAT(sum(TotalDue), '$#,##0.00') as TotalSales 
from [Sales].[SalesOrderHeader]
group by year(OrderDate) , month(OrderDate)
order by YR , MO

-- 2. Top 10 Products by Revenue and Quantity Sold
select top 10 p.name,
        sum(sod.OrderQty) as TotalQty,
        sum(sod.LineTotal) as TotalRevenue
from [Sales].[SalesOrderDetail] sod 
join [Production].[Product] p on sod.ProductID = p.ProductID
group by p.Name 
order by TotalRevenue desc

--3. Sales Performance by Territory (with Avg Order Value)
select st.Name as Territory,
        st.[Group],
        sum(soh.TotalDue) as TotalSales,
        count(soh.SalesOrderID) as OrderCount,
        SUM(soh.TotalDue) / COUNT(soh.SalesOrderID) AS AvgOrderValue
from [Sales].[SalesOrderHeader] soh
join [Sales].[SalesTerritory] st on soh.TerritoryID = st.TerritoryID
group by st.[Group],
         st.Name
order by TotalSales desc 

--4. Top 10 Customers by Revenue (with Name & Territory)
SELECT TOP 10
    c.CustomerID,
    CONCAT(p.FirstName, ' ', p.LastName) AS CustomerName,
    st.Name AS Territory,
    SUM(soh.TotalDue) AS TotalRevenue,
    COUNT(soh.SalesOrderID) AS OrderCount
FROM Sales.Customer c
JOIN Sales.SalesOrderHeader soh
    ON c.CustomerID = soh.CustomerID
JOIN Sales.SalesTerritory st
    ON soh.TerritoryID = st.TerritoryID
left JOIN Person.Person p
    ON c.PersonID = p.BusinessEntityID
GROUP BY
    c.CustomerID,
    p.FirstName,
    p.LastName,
    st.Name
ORDER BY TotalRevenue DESC;

--5. Total Gross Profit Calculation
select sum(s.LineTotal - (s.OrderQty * p.StandardCost)) as Profit
from [Sales].[SalesOrderDetail] s
join [Production].[Product] p on s.ProductID = p.ProductID 
```
