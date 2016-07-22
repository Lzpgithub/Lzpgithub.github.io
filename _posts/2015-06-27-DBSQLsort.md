---
layout: post
title: SQL排序数据
categories: 数据库开发
description: SQL排序数据
keywords: 数据库
---

检索出的数据并不是以纯粹的随机方式显示的。如果不排序，数据一般将以它在底层表中出项的顺序显示。

**子句：**SQL语句由子句构成，有些子句是必需的，而有些是可选的。一个子句通常由一个关键字加上所提供的数据组成。

#### 排序数据

为了明确地排序用SELECT语句检索出的数据，可使用ORDER BY子句。ORDER BY子句取一个或多个列的名字，据此对输出进行排序。

```cpp
SELECT prod_name FROM Products ORDER BY prod_name;
```

这条语句就是指示DBMS软件对prod_name列以字母顺序排序数据，然后显示prod_name列。

ORDER BY子句的位置：在指定一条ORDER BY子句时，应保证它是SELECT语句中最后一条子句，该子句的次序不对将会出现错误消息。

#### 按多个列排序

经常需要按不止一个列进行数据排序。例如，如果要显示雇员清单，可能希望按姓和名排序（首先按姓排序，然后在每个姓钟再按名排序），如果多个雇员具有相同的姓，这样做很有用。

为了按多个列排序，简单指定列名，列名之间用逗号分开即可。

```cpp
SELECT prod_id,prod_price,prod_name FROM Products 
ORDER BY prod_price,prod_name;
```

运行结果为：

![](/images/posts/DataBase/75.png) 

#### 按列位置排序

除了能用列名指出排序顺序外，ORDER BY还支持按相对列位置进行排序：

```cpp
SELECT prod_id,prod_price,prod_name
FROM Products
ORDER BY 2,3;
```

2和3表示表中的第二列和第三列，运行的结果和前面的一样。

#### 指定排序方向

数据排序不限于升序排序（从A到Z），这只是默认的排序顺序。还可以使用ORDER BY子句以降序（从Z到A）顺序的排序。为了进行降序排序，必须指定关键字DESC。

```cpp
SELECT prod_id,prod_price,prod_name
FROM Products 
ORDER BY prod_price DESC;
```

运行结果：

![](/images/posts/DataBase/76.png) 

可以用多个列进行排序，例如以降序排序产品（最贵的在最前面），然后再对产品名排序：

```cpp
SELECT prod_id,prod_price,prod_name
FROM Products 
ORDER BY prod_price DESC,prod_name;
```

DESC关键字只应用到直接位于前面的列名。在上例中，只对prod_price列指定DESC，对prod_name列不指定。因此prod_name列以降序排序，而prod_name列（在每个价格内）仍然按标准的升序排序。因此，**在多个列上降序排序，如果想在多个列上进行降序排序，必须对每个列指定DESC关键字**。

请注意，DESC为DESCENDING的缩写，这两个关键字都可以使用，DESC的反面是ASC（或ASCENDING），在升序排序时可以指定它。但实际上，ASC没有多大用处，因为升序是默认的。