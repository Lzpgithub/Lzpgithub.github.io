---
layout: post
title: SQL高级数据过滤
categories: 数据库开发
description: SQL高级数据过滤
keywords: 数据库
---

为了进行更强的过滤控制，SQL允许给出多个WHERE子句。这些子句可以用两种方式使用，即：以AND子句的方式或OR子句的方式使用。

**操作符**是用来联结或改变WHERE子句中的子句的关键字，也称为逻辑操作符。

#### AND操作符

```cpp
SELECT prod_id,prod_price,prod_name
FROM Products
WHERE prod_id='BNBG01' AND prod_price<=4;
```

#### OR操作符

OR操作符与AND操作符不同，它指示数据库关系系统软件匹配任一条件行。事实上，许多DBMS在OR WHERE子句的第一个条件满足的情况下，不再计算第二个条件（在第一个条件满足时，不管第二个条件是否满足，相应的行都将被检索出来）。

```cpp
SELECT prod_id,prod_price,prod_name
FROM Products
WHERE prod_id='BNBG01' OR prod_price<=4;
```

#### 计算次序

WHERE可包含任意数目的AND和OR操作符。允许两者结合以进行复杂的高级过滤。

```cpp
SELECT prod_name,prod_price
FROM Products
WHERE vend_id='DLL01' OR vend_id='BRS01' AND prod_price>=10;
```

SQL语言在处理OR操作符前，优先处理AND操作符。当SQL看到上述WHERE子句时，它理解为由供应商BRS01制造的任何价格为10美元以上的产品，或者由供应商DLL01制造的任何产品，而不管价格如何。也即是说AND在计算次序中优先级更高。

```cpp
SELECT prod_name,prod_price
FROM Products
WHERE (vend_id='DLL01' OR vend_id='BRS01') AND prod_price>=10;
```

#### IN操作符

IN操作符用来指定条件范围，范围中的每个条件都可以进行匹配。IN是指定的范围是由逗号分隔开的合法值清单，全都括在圆括号中。

```cpp
SELECT prod_name,prod_price
FROM Products
WHERE vend_id IN('DLL01','BRS01')
ORDER BY prod_name;
```

此SELECT语句检索供应商DLL01和BRS01制造的所有产品。其实IN操作符完成的功能和OR相同。

```cpp
SELECT prod_name,prod_price
FROM Products
WHERE vend_id = 'DLL01' OR vend_id = 'BRS01'
ORDER BY prod_name;
```

IN操作符的优点：

1.在使用长的合法选项清单时，IN操作符的语法更清楚且直观；

2.在使用IN时，计算的次序更容易管理（因为使用的操作符更少）；

3.IN操作符一般比OR操作符清单执行更快；

4.IN的最大优点是可以包含其他SELECT语句，使得能够更动态地建立WHERE子句。

#### NOT操作符

WHERE子句中的NOT操作符有且只有一个功能，那就是否定它之后所跟的任何条件。因为NOT从不自己使用（它总是与其他操作符在一起使用），它的语法与其他操作符有所不同。与其他操作符不一样，NOT可以用在要过滤的列的前面，也可以在后面。

列出除DLL01之外的所有供应商制造的产品：

```cpp
SELECT prod_name
FROM Products
WHERE NOT vend_id = 'DLL01'
ORDER BY prod_name;
```

这个例子也可以使用<>操作符来完成：

```cpp
SELECT prod_name
FROM Products
WHERE vend_id <> 'DLL01'
ORDER BY prod_name;
```

在更复杂的子句中，NOT是非常有用的，例如在与IN操作符联合使用时，NOT使得找出与条件列表不匹配的行非常简单。



