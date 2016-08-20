---
layout: post
title: 使用数据处理函数
categories: 数据库开发
description: 使用数据处理函数
keywords: 数据库
---

#### 函数带来的问题

与其他大多数计算机语言一样，SQL支持利用函数来处理数据。函数一般是在数据上执行的，它给数据的转换和处理提供了方便。

与几乎所有DBMS都等同地支持SQL语句（如SELECT)不同，每一个DBMS都特定的函数。事实上，只有少数几个函数被所有主要的DBMS等同地支持。虽然所有类型的函数一般都可以在每个DBMS中使用，但各个函数的实现可能有很大的不同。

#### 文本处理函数

如使用UPPER()函数将文本转换为大写：

```cpp
SELECT vend_name,UPPER(vend_name) AS
vend_name_upcase
FROM Vendors
ODERBY BY vend_name;
```

![](/images/posts/DataBase/90.png)

![](/images/posts/DataBase/91.png)

如果Customers表中有一个顾客Kids Place，其联系名为Michelle Green。但如果这是输入错误，次联系名实际应该是Michael Green，怎么办？显然，按正确的联系名搜索不会返回数据，如下所示：

```cpp
SELECT cust_name,cust_contact
FROM Customers
WHERE cust_contact='Machael Green';
```

![](/images/posts/DataBase/92.png)

现在使用SOUNDEX()函数进行搜索，它能够匹配所有发音类似于Machael Green的联系名：

```cpp
SELECT cust_name,cust_contact
FROM Customers
WHERE SOUNDEX(cust_contact)=SOUNDEX('Machael Green');
```


![](/images/posts/DataBase/93.png)

#### 日期和时间处理函数

```cpp
SELECT order_num
FROM Orders
WHERE DATEPART(yy,order_date)=2004;
```

![](/images/posts/DataBase/94.png)

#### 数值处理函数

数值处理函数仅处理数值数据。这些函数一般主要用于代数、三角或几何运算：


![](/images/posts/DataBase/95.png)




