---
layout: post
title: 使用视图
categories: 数据库开发
description: 使用视图
keywords: 数据库
---

#### 视图

视图是虚拟的表。与包含数据的表不一样，视图只包含使用时动态检索数据的查询。

理解视图先举个例子：

```cpp
SELECT cust_name,cust_contact
FROM Customers,Orders,OrderItems
WHERE Customers.cust_id=Orders.cust_id
	AND OrderItems.order_num=Orders.order_num
	AND prod_id='RGAN01';
```

此查询用来检索订购了某个特定产品的客户。任何需要这个数据的人都必须理解相关表的结构，并且知道如何创建查询和对表进行联结。为了检索其他产品（或多个产品）的相同数据，必须修改最后的WHERE子句。

现在假设可以把整个查询包装成一个名为ProductCustomers的虚拟表，则可以如下轻松地检索出相同的数据。

```cpp
SELECT cust_name,cust_contact
FROM ProductCustomers
WHERE prod_id='RGAN01';
```

这就是视图的作用。ProductCustomers是一个视图，作为视图，它不包含任何列或数据，它包含的是一个查询。

#### 为什么使用视图

1、重用SQL语句。

2、简化复杂的SQL操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节。

3、使用表的组成部分而不是整个表。

4、保护数据。可以给用户授予表的特定部分的访问权限而不是整个表的访问权限。

5、更改数据格式和表示。视图可返回与地层表的表示和格式不同的数据。

**视图仅仅是用来查看存储在别处的数据的一种设施。视图本身不包含数据，因此它们返回的数据集是从其他表中检索出来的。在添加或更改这些表中的数据时，视图将返回改变过的数据**


#### 创建和删除视图

创建视图：

```cpp
CREATE VIEW viewname;
```

删除视图：

```cpp
DROP VIEW viewname;
```

#### 利用视图简化联结

```cpp
CREATE VIEW ProductCustomers AS
SELECT cust_name,cust_contact,prod_id
FROM Customers,Orders,OrderItems
WHERE Customers.cust_id=Orders.cust_id
AND OrderItems.order_num=Orders.order_num;
```

这条语句创建一个名为ProductCustomers的视图，它联结三个表，以返回已订购了任意产品的所有客户的列表。如果执行：

```cpp
SELECT * FROM ProductCustomers
```

将列出订购了任意产品的客户。

例如：

```cpp
SELECT cust_name,cust_contact
FROM ProductCustomers
WHERE prod_id='RGAN01';
```

![](/images/posts/DataBase/121.png)

#### 用视图重新格式化检索出的数据

```cpp
CREATE VIEW VendorLocations AS
SELECT RTRIM(vend_name)+'('+RTRIM(vend_country)
+')' AS vend_title
FROM Vendors;
```

```cpp
SELECT *
FROM VendorLocations;
```

![](/images/posts/DataBase/122.png)

#### 用视图过滤不想要的数据

```cpp
CREATE VIEM CustomerEMailList AS
SELECT cust_id,cust_name,cust_email
FROM Customers
WHERE cust_email IS NOT NULL;
```

```cpp
SELECT *
FROM CustomerEmailList;
```

![](/images/posts/DataBase/123.png)

#### 使用视图与计算字段

```cpp
SELECT prod_id,
	quantity,
	item_prict,
	quantity*item_price AS expanded_price
FROM OrderItems
WHERE order_num=20008;
```

![](/images/posts/DataBase/124.png)

为将其转换为一个视图，如下进行：

```cpp
CREATE VIEM OrderItemsExpanded AS
SELECT order_num,
	prod_id,
	quantity,
	item_price,
	quantity*item_price AS expanded_price
FROM OrderItems;
```

```cpp
SELECT *
FROM OrderItemsExpanded
WHERE order_num=20008;
```

![](/images/posts/DataBase/125.png)