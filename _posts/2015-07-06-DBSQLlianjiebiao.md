---
layout: post
title: 联结表
categories: 数据库开发
description: 联结表
keywords: 数据库
---

分解数据为多个表能更加有效地存储，更方便地处理，并且具有更大的可伸缩性。

如果数据存储在多个表中，怎样用单条SELECT语句检索出数据？

答案是使用联结。简单地说，联结是一种机制，用来在一条SELECT语句中关联表，因此称之为联结。使用特殊的语法，可以联结多个表返回一组输出，联结在运行时关联表中正确行。

#### 创建联结

```cpp
SELECT vend_name,prod_name,prod_price
FROM Vendors,Products
WHERE Vendors.vend_id=Products.vend_id;
```

![](/images/posts/DataBase/108.png)

![](/images/posts/DataBase/109.png)

笛卡尔积：由没有联结条件的表关系返回的结果为笛卡尔积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。

```cpp
SELECT vend_name,prod_name,prod_price
FROM Vendors,Products;
```

![](/images/posts/DataBase/110.png)

![](/images/posts/DataBase/111.png)

#### 内部联结

目前为止所用的联结称为等值联结，它基于两个表之间的相等测试。这种连接也称为内部联结。其实，对于这种联结可以使用稍微不同的语法来明确指定联结的类型。下面的SELECT语句返回与前面例子完全相同的数据：

```cpp
SELECT vend_name,prod_name,prod_price
FROM Vendors INNER JOIN Products
ON Vendors.vend_id=Products.vend_id;
```

#### 联结多个表

```cpp
SELECT prod_name,vend_name,prod_price,quantity
FROM OrderItems,Products,Vendors
WHERE Products.vend_id=Vendors.vend_id
	AND OrderItems.prod_id=Products.prod_id
	AND order_num=20007;
```

![](/images/posts/DataBase/112.png)

我们使用子查询：

```cpp
SELECT cust_name,cust_contact
FROM Customers
WHERE cust_id IN (SELECT cust_id
	FROM Orders
	WHERE order_num	IN (SELECT order_num
		FROM OrderItems
		WHERE prod_id='RGAN01'));
```

改成使用联结方式：

```cpp
SELECT cust_name,cust_contact
FROM Customers,Orders,OrderItems
WHERE Customers.cust_id=Orders.cust_id
	AND OrderItem.order_num=Orders.order_num
	AND prod_id='RGAN01';
```

![](/images/posts/DataBase/113.png)