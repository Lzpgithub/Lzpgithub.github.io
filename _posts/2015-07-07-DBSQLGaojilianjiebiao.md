---
layout: post
title: 创建高级联结
categories: 数据库开发
description: 创建高级联结
keywords: 数据库
---


#### 使用表别名

```cpp
SELECT cust_name,cust_contact
FROM Customers AS C,Orders AS O,OrderItmes AS OI
WHERE C.cust_id=O.cust_id
AND OI.order_num=O.order_num
AND prod_id='RGAN01';
```

#### 自联结

假设想发送一封信件给为Jim Jones所在的公司工作的所有用户。此查询要求首先找出Jim Jones工作的公司，然后找出为此公司工作的客户。

首先使用子查询的方法：

```cpp
SELECT cust_id,cust_name,cust_contact
FROM Customers
WHERE cust_name=(SELECT cust_name
	FROM Customers
	WHERE cust_constact ='Jim Jones');
```

![](/images/posts/DataBase/114.png)

使用联结的相同查询：

```cpp
SELECT c1.cust_id,c1.cust_name,c1.cust_contact
FROM Customers As c1,Customers AS c2
WHERE c1.cust_name=c2.cust_name
AND c2.cust_contact='Jim Jones';
```

![](/images/posts/DataBase/114.png)

#### 自然联结

无论何时对表进行联结，应该至少有一个列出现在不止一个表中（被联结的列）。标准的联结返回所有数据，甚至相同的列多次出现。自然联结排除多次出现，使每个列只返回一次。

自然连接是这样一种联结，其中你只能选择那些唯一的列。这一般是通过对表使用通配符（SELECT*）。

```cpp
SELECT C.* O.order_num,O.order_date,OI.prod_id,
OI.quantity,OI.item_price
FROM Customers AS C,Orders AS O,OrderItems AS OI
WHERE C.cust_id=O.cust_id
AND OI.order_num=O.order_num
AND prod_id='RGAN01';
```

#### 外部联结

使用一个简单的内部联结，检索所有客户及其订单：

```cpp
SELECT Customers.cust_id,Orders.order_num
FROM Customers INNER JOIN Orders
ON Customers.cust_id=Orders.cust_id;
```

外部联结语法类似，检索所有客户，包括那些没有订单的客户：

```cpp
SELECT Customers.cust_id,Orders.order_num
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id=Orders.cust_id;
```

![](/images/posts/DataBase/115.png)

#### 使用带聚集函数的联结

如果要检索所有客户及每个客户所下的订单数，下面使用COUNT()函数的代码可完成次工作：

```CPP
SELECT Customers.cust_id,COUNT(Orders.order_num) AS
num_ord
FROM Customers INNER JOIN Orders
ON Customer.cust_id=Orders.cust_id
GROUP BY Customers.cust_id;
```

![](/images/posts/DataBase/116.png)


