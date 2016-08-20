---
layout: post
title: 插入数据
categories: 数据库开发
description: 插入数据
keywords: 数据库
---

INSERT是用来插入（或添加）行到数据库表的。插入可以使用几种方式：

1、插入完整的行；

2、插入行的一部分；

3、插入某些查询的结果；

#### 插入完整的行

```cpp
INSERT INTO Customers
VALUES('10000006',
	'Toy Land',
	'123 Any Street',
	'New York',
	'NY',
	'11111',
	'USA',
	NULL,
	NULL);
```

编写更加安全INSERT语句（不过更繁琐）的方法如下：

```cpp
INSERT INTO Customers(
	cust_name,
	cust_address,
	cust_city,
	cust_state,
	cust_zip,
	cust_country,
	cust_contact,
	cust_email)
VALUES('10000006',
	'Toy Land',
	'123 Any Street',
	'New York',
	'NY',
	'11111',
	'USA',
	NULL,
	NULL);
```

VALUES中的第一个值对应于第一个指定的列名。第二个值对应于第二个列名等等。

#### 插入部分行

```cpp
INSERT INTO Customers(
	cust_name,
	cust_address,
	cust_city,
	cust_state,
	cust_zip,
	cust_country)
VALUES('10000006',
	'Toy Land',
	'123 Any Street',
	'New York',
	'NY',
	'11111',
	'USA');
```

如果表的定义允许，则可以在INSERT操作中省略某些列，省略的列必须满足以下某个条件：

1、该列定义为允许NULL值（无值或空值）

2、在表定义中给出默认值。这表示如果不给出值，将使用默认值。

#### 插入检索出的数据

```cpp
INSERT INTO Customers(
	cust_name,
	cust_address,
	cust_city,
	cust_state,
	cust_zip,
	cust_country,
	cust_contact,
	cust_email)
SELECT cust_name,
	cust_address,
	cust_city,
	cust_state,
	cust_zip,
	cust_country,
	cust_contact,
	cust_email
FROM CustNew;
```

#### 从一个表复制到另一个表

```cpp
SELECT *
INTO CustCopy
FROM Customers;
```

这条语句创建一个名为CustCopy的新表，并把Customers表的整个内容复制到新表中。





