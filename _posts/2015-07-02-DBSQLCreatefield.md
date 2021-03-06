---
layout: post
title: 创建计算字段
categories: 数据库开发
description: 创建计算字段
keywords: 数据库
---

#### 计算字段

存储在数据库表中的数据一般不是应用程序所需要的格式。下面举几个例子：

（1）.如果想显示公司名，同时还想显示公司的地址，但这两个信息一般包含在不同的表列中。

（2）.城市、州和邮政编码存储在不同的列中，当邮件标签打印程序却需要把它们作为一个恰当格式的字段检索出来。

（3）.列数据是大小写混合的，但报表程序需要把所有数据按大写表示出来。

（4）.物品订单表存储物品的价格和数量，但不需要存储每个物品的总价格（用价格乘以数量即可）。为打印发票，需要物品的总价格。

（5）.需要根据表数据进行总数、平均数计算和其他计算。

在上述每个例子中，存储在表中的数据都不是应用程序所需要的。我们需要直接从数据库中检索出转换、计算或格式化的数据；而不是检索出数据，然后在客户机应用程序中重新格式化。

**计算字段并不实际存在数据库表中，计算字段是在SELECT语句内创建的**。

字段基本与列的意思相同，经常互换使用，不过数据库列一般称为列。

重要的是要注意到，只有数据库知道SELECT语句中哪些列时实际的列表，哪些列是计算字段。从客户机（如应用程序）的角度来看，计算字段的数据是以其他列的数据相同的方式返回的。

#### 拼接字段

Vendors表包含供应商和位置信息，假如要生成一个供应商报表，需要在格式化的名称中列出供应商的位置。此报表需要单个值，而表中数据存储在两个列vend_name和vend_country中。此外需要用括号将vend_country括起来，这些东西都没有存储在数据库表中。

解决办法就是把两个列拼接起来。在SQL中的SELECT语句中，可适用一个特殊的操作符来拼接两个列。此操作符可用加号（+）后两个竖杠（||）表示。

**拼接：**就是将值联结到一起构成单个值。

```cpp
SELECT vend_name + '(' + vend_country + ')'
FROM Vendors
ORDER BY vend_name;
```

![](/images/posts/DataBase/86.png) 

下面是相同的语句，但使用的是||语法：

```cpp
SELECT vend_name || '(' || vend_country || ')'
FROM Vendors
ORDER BY vend_name;
```

上面两个SELECT语句连接一下元素：

1.存储在vend_name列中的名字；

2.包含一个空格和一个开圆括号的串；

3.存储在vend_country列中的国家；

4.包含一个闭圆括号的串。

从上述输出中可以看到，SELECT语句返回包含上述四个元素的单个列（计算字段）。


上述SELECT语句返回的输出可以看出，一个计算字段的两个列用空格填充。许多数据库保存填充为列宽的文本值。为正确返回格式化的数据，必须去掉这些空格。这可以使用SQL的RTRIM()函数来完成，如下所示：

```cpp
SELECT RTRIM(vend_name) + '(' + vend_country + ')'
FROM Vendors
ORDER BY vend_name;
```

![](/images/posts/DataBase/87.png) 

RIRIM()函数去掉值右边的所有空格，通过使用RTRIM()，各个列都进行了整理。

#### 使用别名

从前面的输出中可以看到，SELECT语句拼接地址字段工作得很好。但次新计算列的名字是什么呢？实际上它没有名字，它只是一个值。但是一个未命名的列不能用于客户机应用中，因为客户机没有办法引用它。

为了结局这个问题，SQL支持列别名。列别名是一个字段或值得替换名。别名用AS关键字赋予。请看下面的SELECT语句：

```cpp
SELECT RTRIM(vend_name)+'('+RTRIM(vend_country)+')' AS vend_title 
FROM Vendors
ORDER BY vend_name;
```

输出结果为：

![](/images/posts/DataBase/88.png)

使用下面相同语句可以得到相同的结果：

```cpp
SELECT RTRIM(vend_name)||'('||RTRIM(vend_country)||')' AS vend_title 
FROM Vendors
ORDER BY vend_name;
```

AS vend_title指示SQL创建一个包含指定计算的名为vend_title的计算字段。

#### 执行算术计算

计算字段的另一常见用途是对检索出的数据进行算术计算。例如，Orders表包含收到的所有订单，OrderItems表包含每个订单中的各项物品。下面SQL语言检索订单号20008中的所有物品：

```cpp
SELECT prod_id,quantity,item_price
FROM OrderItems
WHERE order_num = 20008;
```

item_price列包含订单中每项物品的单价，如下汇总物品的价格（单价乘以订购数量）：

```cpp
SELECT prod_id,quantity,item_price,
quantity*item_price AS expanded_price
FROM OrderItems
WHERE order_num=20008;
```

![](/images/posts/DataBase/89.png)

输出中显示的expanded_price列位一个计算字段。此计算为quantity*item_price。客户机现在可以使用这个新的计算列，就像其他列一样。





