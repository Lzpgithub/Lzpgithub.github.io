---
layout: post
title: 更新和删除数据
categories: 数据库开发
description: 更新和删除数据
keywords: 数据库
---

#### 更新数据

```cpp
UPDATE Customers
SET cust_email='kim@thetoystore.com'
WHERE cust_id='10000005'
```

```cpp
UPDATE Customers
SET cust_contact='Sam Roberts',
	cust_email='sam@toyland.com'
WHERE cust_id='1000006';
```

为了删除某个列的值，可设置它为NULL

```cpp
UAPDATE Customers
SET cust_email=NULL
WHERE cust_id='10000005';
```

#### 删除数据

使用DELETE语句：

1、从表中删除特定的行；

2、从表中删除所有行；

```cpp
DELETE FROM Customers
WHERE cust_id='10000006'
```






