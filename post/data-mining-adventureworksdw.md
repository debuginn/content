---
title: "数据仓库与数据挖掘 使用SQL语句实现AdventureWorksDW数据仓库的多维数据分析"
date: 2019-03-27T21:40:26+08:00
author: "Meng小羽"
authorLink: "https://debuginn.com"
authorEmail: "debuginn@icloud.com"
keywords: "数据仓库与数据挖掘"
comments: true
weight: 0
tags: ["数据仓库与数据挖掘"]
categories: ["debuginn"]
---

## 准备工作

AdventureWork各种版本下载链接：

此操作数据库版本为：2014版本。

## 切片操作

进行切片操作切片。选择地点维、产品维和时间维查看2012年3月份的销售额

```sql
SELECT 
	DimProduct.EnglishProductName AS 产品名称, 
	DimSalesTerritory.SalesTerritoryRegion AS 产品地区,
	MONTH(FactInternetSales.OrderDate) AS 月份,
	SUM(FactInternetSales.SalesAmount) AS 销售额
FROM DimProduct, DimSalesTerritory, FactInternetSales
WHERE
	DimProduct.ProductKey = FactInternetSales.ProductKey
	AND DimSalesTerritory.SalesTerritoryKey = FactInternetSales.SalesTerritoryKey
	AND MONTH(FactInternetSales.OrderDate) = 3
	AND YEAR(FactInternetSales.OrderDate) = 2012
GROUP BY 
	DimProduct.EnglishProductName,
	DimSalesTerritory.SalesTerritoryRegion,
	MONTH(FactInternetSales.OrderDate);
```

![result](https://static.debuginn.com/202303252142124.png)

## 切块操作

切块操作切块。选择地点维、产品维和时间维查看2011年3月份和4月份的销售额

```sql
SELECT 
	DimProduct.EnglishProductName AS 产品名称, 
	DimSalesTerritory.SalesTerritoryRegion AS 产品地区,
	MONTH(FactInternetSales.OrderDate) AS 月份,
	SUM(FactInternetSales.SalesAmount) AS 销售额
FROM DimProduct, DimSalesTerritory, FactInternetSales
WHERE
	DimProduct.ProductKey = FactInternetSales.ProductKey
	AND DimSalesTerritory.SalesTerritoryKey = FactInternetSales.SalesTerritoryKey
	AND MONTH(FactInternetSales.OrderDate)BETWEEN 5 and 7
	AND YEAR(FactInternetSales.OrderDate) = 2012
GROUP BY 
	DimProduct.EnglishProductName,
	DimSalesTerritory.SalesTerritoryRegion,
	MONTH(FactInternetSales.OrderDate);
```

![切块操作切块](https://static.debuginn.com/202303252144018.png)

## 旋转操作

旋转操作旋转。选择地点维、产品维和时间维,以地区维为主视图查看销售额

```sql
SELECT 
	DimSalesTerritory.SalesTerritoryRegion AS 产品地区,
	DimProduct.EnglishProductName AS 产品名称, 
	YEAR(FactInternetSales.OrderDate) AS 年份,
	MONTH(FactInternetSales.OrderDate) AS 月份,
	SUM(FactInternetSales.SalesAmount) AS 销售额
FROM 
	-- 产品表
	DimProduct,
	-- 销售地区表 
	DimSalesTerritory, 
	-- 销售量
	FactInternetSales
WHERE
	DimProduct.ProductKey = FactInternetSales.ProductKey
	AND DimSalesTerritory.SalesTerritoryKey = FactInternetSales.SalesTerritoryKey
	AND YEAR(FactInternetSales.OrderDate) = 2011
GROUP BY 
	DimProduct.EnglishProductName,
	DimSalesTerritory.SalesTerritoryRegion,
	YEAR(FactInternetSales.OrderDate),
	MONTH(FactInternetSales.OrderDate);
```

![旋转操作旋转](https://static.debuginn.com/202303252144795.png)

## 旋转+切块

```sql
SELECT 
	DimSalesTerritory.SalesTerritoryRegion AS 产品地区,
	DimProduct.EnglishProductName AS 产品名称, 
	YEAR(FactInternetSales.OrderDate) AS 年份,
	MONTH(FactInternetSales.OrderDate) AS 月份,
	SUM(FactInternetSales.SalesAmount) AS 销售额
FROM 
	-- 产品表
	DimProduct,
	-- 销售地区表 
	DimSalesTerritory, 
	-- 销售量
	FactInternetSales
WHERE
	DimProduct.ProductKey = FactInternetSales.ProductKey
	AND DimSalesTerritory.SalesTerritoryKey = FactInternetSales.SalesTerritoryKey
	AND YEAR(FactInternetSales.OrderDate) BETWEEN 2011 AND 2014
GROUP BY 
	DimProduct.EnglishProductName,
	DimSalesTerritory.SalesTerritoryRegion,
	YEAR(FactInternetSales.OrderDate),
	MONTH(FactInternetSales.OrderDate);
```

![旋转+切块](https://static.debuginn.com/202303252145242.png)

## 上钻操作

上钻。选择地点维、产品维和时间维查看不同年份的销售额

```sql
SELECT 
	DimProduct.EnglishProductName AS 产品名称, 
	DimSalesTerritory.SalesTerritoryRegion AS 产品地区,
	MONTH(FactInternetSales.OrderDate) AS 月份,
	SUM(FactInternetSales.SalesAmount) AS 销售额
FROM DimProduct, DimSalesTerritory, FactInternetSales
WHERE
	DimProduct.ProductKey = FactInternetSales.ProductKey
	AND DimSalesTerritory.SalesTerritoryKey = FactInternetSales.SalesTerritoryKey
GROUP BY 
	DimProduct.EnglishProductName,
	DimSalesTerritory.SalesTerritoryRegion,
	MONTH(FactInternetSales.OrderDate);
```

![上钻](https://static.debuginn.com/202303252146458.png)

## 下钻操作

下钻。选择地点维、产品维和时间维查看不同日期的销售额

```sql
SELECT 
	DimProduct.EnglishProductName AS 产品名称, 
	DimSalesTerritory.SalesTerritoryRegion AS 产品地区,
	MONTH(FactInternetSales.OrderDate) AS 月份,
	SUM(FactInternetSales.SalesAmount) AS 销售额
FROM DimProduct, DimSalesTerritory, FactInternetSales
WHERE
	DimProduct.ProductKey = FactInternetSales.ProductKey
GROUP BY 
	DimProduct.EnglishProductName,
	DimSalesTerritory.SalesTerritoryRegion,
	MONTH(FactInternetSales.OrderDate);
```
