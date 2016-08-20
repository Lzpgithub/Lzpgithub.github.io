---
layout: post
title: 使用子查询
categories: 数据库开发
description: 使用子查询
keywords: 数据库
---

#### 利用子查询进行过滤

```cpp
SELECT order_num
FROM OrderItems
WHERE prod_id='RGAN01';
```

![](/images/posts/DataBase/105.png)

```cpp
SELECT cust_id
FROM Orders
WHERE order_num IN(20007,20008);
```

![](/images/posts/DataBase/106.png)

变为两个子查询：

```cpp
SELECT cust_id
FROM Orders
WHERE order_num IN (SELECT order_num)
					FROM OrderItems
					WHERE prod_id='RGAN01');
```

![](/images/posts/DataBase/106.png)

```cpp
SELECT cust_name,cust_contact
FROM Customers
WHERE cust_id IN (SELECT cust_id
	FROM Orders
	WHERE order_num	IN (SELECT order_num
		FROM OrderItems
		WHERE prod_id='RGAN01'));
```

#### 作为计算字段使用子查询

```cpp
SELECT cust_name
cust_state,
(SELECT COUNT(*)
		FROM Orders
		WHERE Orders.cust_id=Customers.cust_id) AS
orders
FROM Customers
ORDER BY cust_name;
```

![](/images/posts/DataBase/107.png)

Orders.cust_id=Customers.cust_id：因为两张表中的字段都是同样的名字，因此为了防止混淆而这样写。