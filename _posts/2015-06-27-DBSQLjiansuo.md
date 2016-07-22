---
layout: post
title: SQL检索数据
categories: 数据库开发
description: SQL检索数据
keywords: 数据库
---

#### SELECT语句

SQL中SELECT语句的用途就是从一个或多个表中检索信息。

##### 检索单个列

例如：

```cpp
SELECT prod_name FROM Products;
```

上述语句就是利用SELECT语句从Products表中检索一个名为prod_name的列。多条SQL语句必须以分号“；”分隔，同时SQL语句不区分大小写。

##### 检索多个列

要想从一个表中检索多个列，使用相同的SELECT语句，唯一的不同是必须在SELECT关键字后给出多个列名。

```cpp
SELECT prod_id,prod_name,prod_price FROM Products;
```

##### 检索所有列

除了指定所需的列外，SELECT语句还可以检索所有的列而不必逐个列出它们，这可以通过在实际列名的位置使用星号**（\*）**，如下所示：

```cpp
SELECT * FROM Products;
```





