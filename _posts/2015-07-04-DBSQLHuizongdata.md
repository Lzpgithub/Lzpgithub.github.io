---
layout: post
title: 汇总数据
categories: 数据库开发
description: 汇总数据
keywords: 数据库
---

#### 聚集函数

1、确定表中行数（或者满足某个条件或包含某个特定值的行数）

2、获得表中行组的和

3、找出表列（或所有行某些特定的行）的最大值、最小、平均值。

聚集函数运行在行组上，计算和返回单个值得函数。

![](/images/posts/DataBase/96.png)

#### AVG()函数

通过对表中行数计数并计算特定列值之和，求得该列的平均值。

```cpp
SELECT AVG(prod_price) AS avg_price
FROM Products;
```

![](/images/posts/DataBase/97.png)

```cpp
SELECT AVG(prod_price) AS avg_price
FROM Products;
WHERE vend_id='DLL01';
```

#### COUNT()函数

COUNT()函数有两种使用方式：

1、使用COUNT(*)对表中行的数目进行计数，不管表列中包含的是空值(NULL)还是非空值。

2、使用COUNT(column)对特定列中具有值得行进行计数，忽略NULL值。

例如返回Customers表中客户的总数：

```cpp
SELECT COUNT(*) AS num_cust
FROM Customers;
```

例如只对具有电子邮件地址的客户计数：

```cpp
SELECT COUNT(cust_email) AS num_cust
FROM Customers;
```

#### MAX和MIN函数

```cpp
SELECT MAX(prod_price) AS max_price
FROM Products;
```


```cpp
SELECT MIN(prod_price) AS min_price
FROM Products;
```

#### SUM函数

返回指定列值得和：

```cpp
SELECT SUM(quantity) AS items_ordered
FROM OrderItems
WHERE order_num=2005;
```

```cpp
SELECT SUM(item_price*quantity) AS items_ordered
FROM OrderItems
WHERE order_num=2005;
```

#### 聚集不同值

使用AVG函数返回特定供应商提供的产品的平均价格。但使用了DISTINCT参数，因此平均值只会考虑各个不同的价格：

```cpp
SELECT AVG(DISTINCT prod_price) AS avg_price
FROM Products
WHERE vend_id='DLL01';
```

#### 组合聚集函数

```cpp
SELECT COUNT(*) AS num_items,
MIN(prod_price) AS price_min,
MAX(prod_price) AS price_max,
AVG(prod_price) AS price_avg FROM Products;
```

![](/images/posts/DataBase/98.png)