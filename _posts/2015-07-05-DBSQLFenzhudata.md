---
layout: post
title: 分组数据
categories: 数据库开发
description: 分组数据
keywords: 数据库
---

#### 创建分组

分组是在SELECT语句的GROUP BY子句中建立的。

```cpp
SELECT vend_id,COUNT(*) AS num_prods
FROM Products
GROUP BY vend_id;
```

![](/images/posts/DataBase/99.png)

从输出中可以看到，供应商BR01有3个产品，供应商DLL01有4个产品，而供应商FNG01有2个产品。

GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前。

#### 过滤分组

```cpp
SELECT cust_id,count(*) AS orders
FROM Orders
GROUP BY cust_id
HAVING COUNT(*) >= 2;
```

![](/images/posts/DataBase/100.png)

这条SELECT语句的前3行类似于上面的语句。最后一行增加了HAVING子句，它过滤COUNT(*)>=2（两个以上的订单）的那些分组。

HAVING和WHERE的差别：WHERE在数据分组前进行过滤，HAVING在数据分组后进行过滤。WHERE排除的行不包括在分组中。这可能会改变计算值，从而影响HAVING子句中基于这些值过滤掉的分组。

```cpp
SELECT vend_id,COUNT(*) AS num_prods
FROM Products
WHERE prod_price>=4
GROUP BY vend_id
HAVING COUNT(*)>=2;
```

![](/images/posts/DataBase/101.png)

```cpp
SELECT vend_id,COUNT(*) AS num_prods
FROM Products
GROUP BY vend_id
HAVING COUNT(*)>=2;
```

![](/images/posts/DataBase/102.png)

#### 分组和排序

```cpp
SELECT order_num,COUNT(*) AS items
FROM OrderItems
GROUP BY order_num
HAVING COUNT(*) >= 3
ORDER BY items,order_num;
```

![](/images/posts/DataBase/103.png)

SELECT子句顺序：

![](/images/posts/DataBase/104.png)