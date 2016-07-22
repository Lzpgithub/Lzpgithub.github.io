---
layout: post
title: SQL过滤数据
categories: 数据库开发
description: SQL过滤数据
keywords: 数据库
---

如何使用SELECT语句的WHERE子句指定搜索条件。

#### 使用WHERE子句

数据库表一般包含大量的数据，很少需要检索表中所有行。通常根据特定操作或报告的需要提取数据的子集。只检索所需数据需要指定搜索条件，搜索条件也称为过滤条件。

在SELECT语句中，数据根据WHERE子句中指定的搜索条件进行过滤。WHERE子句在表名（FROM子句）之后给出，如下所示：

```cpp
SELECT prod_name,prod_price 
FROM Products 
WHERE prod_price = 3.49;
```

程序运行结果为：

![](/images/posts/DataBase/77.png) 

WHERE子句的位置，在同时使用ORDER BY 和WHERE子句时，应该让ORDER BY位于WHERE之后，否则将会产生错误。


#### WHERE子句操作符

我们在关于相等的测试时看到了第一个WHERE子句，它确定一个列是否包含特定的值。SQL支持下面表中列出的所有条件操作符：

![](/images/posts/DataBase/78.png) 


#### 检测单个单值

列出价格小于10美元的所有产品：

```cpp
SELECT prod_name,prod_price 
FROM Products
WHERE prod_price < 6;
```

![](/images/posts/DataBase/79.png)

#### 不匹配检查

列出不是由供应商BR01制造的所有产品：

```cpp
SELECT prod_id,prod_name
FROM Products
WHERE prod_id <> 'BR01';
```

程序运行结果：

![](/images/posts/DataBase/80.png)

也可使用“！=”来替代操作符“<>”。

#### 范围值检查

为了检查某个范围的值，可以使用BETWEEN操作符。其语法与其他WHERE子句的操作符稍有不同，因为它需要两个值，即范围的开始值和结束值。例如BETWEEN操作符可用来检索价格在5美元和10美元之间或日期在指定的开始日期和结束日期之间的所有产品。

```cpp
SELECT prod_name,prod_price
FROM Products
WHERE prod_price BETWEEN 5 AND 10;
```

程序运行结果：

![](/images/posts/DataBase/81.png)

#### 空值检查

在创建表时，表设计人员可以指定其中的列是否可以不包含值。在一个列不包含值时，称其为包含空值NULL。

NULL表示无值，它与字段包含0、空字符串或仅仅包含空格不同。

```cpp
SELECT prod_name
FROM Products
WHERE prod_price IS NULL;
```