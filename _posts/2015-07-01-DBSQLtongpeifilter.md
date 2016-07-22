---
layout: post
title: SQL通配符进行过滤
categories: 数据库开发
description: SQL通配符进行过滤
keywords: 数据库
---

#### LIKE操作符

前面介绍的所有操作符都是针对已知值进行过滤。不管是匹配一个还是多个值，测试大于还是小于已知值，或者检查某个范围值，共同点是过滤中使用的值都是已知的。但是这种过滤方法并不是任何时候都好用。

例如，怎样搜索产品名中包含文本bean bag的所有产品？用简单的比较操作符肯定不行，必须使用通配符。利用通配符可创建比较特定数据的搜索模式。

**通配符：**用来匹配值得一部分的特殊字符。

**搜索模式：**由字面值、通配符或两者组合构成的搜索条件。

通配符本身实际是SQL的WHERE子句中有特殊含义的字符，SQL支持几种通配符。为在搜索子句中使用通配符，必须使用LIKE操作符。LIKE指示DBMS，后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较。

**通配符搜索只能用于文本字段（串），非文本数据类型字段不能使用通配符搜索。**

#### 百分号（%）通配符

最常用的通配符是百分号（%），在搜索串中，%表示任何字符出现任意次数。例如找出以词Fish起头的产品，可以发布一下SELECT语句：

```cpp
SELECT prod_id,prod_name
FROM Products
WHERE prod_name LIKE 'Fish%'
```

程序运行结果：

![](/images/posts/DataBase/82.png) 

此例子使用了搜索模式‘Fish%’。在执行这条子句时，将检索任意以Fish起头的词。%告诉DBMS接受Fish之后的任意字符，不管它有多少个字符。根据DBMS的不同及其配置，搜索可以是区分大小写的，如果区分大小写，‘fish%’与Fish bean bag toy将不匹配。

通配符可在搜索模式中任意位置使用，并且可以使用多个通配符。

```cpp
SELECT prod_id,prod_name
FROM Products
WHERE prod_name LIKE '%bean bag%'
```

程序运行结果：

![](/images/posts/DataBase/83.png) 

搜索模式‘%bean bag%’表示匹配任何位置包含文本bean bag的值，而不论它之前或之后出现什么字符。

通配符也可以出现在搜索模式的中间，虽然这样做不太有用。下面的例子找出以F起头、以y结尾的所有产品：

```cpp
SELECT prod_id,prod_name
FROM Products
WHERE prod_name LIKE 'F%y'
```

#### 下划线（__）通配符

下划线的用途与%一样，但下划线只匹配单个字符而不是多个字符。

```cpp
SELECT prod_id,prod_name
FROM Products
WHERE prod_name LIKE '__ inch teddy bear'
```

程序运行结果：

![](/images/posts/DataBase/84.png)

此WHERE子句中的搜索模式给出了后面跟有文本的两个通配符。结果只显示匹配搜索模式的行：第一行中下划线匹配12，第二行匹配18，“8 inch teddy bear”产品没有匹配，因为搜索模式要求匹配两个通配符而不是一个。对照一下下面的SELECT语句使用%通配符返回三行产品：

```cpp
SELECT prod_id,prod_name
FROM Products
WHERE prod_name LIKE '% inch teddy bear'
```

程序运行结果：

![](/images/posts/DataBase/85.png)

#### 方括号（[]）通配符

方括号（[]）通配符用来指定一个字符集，它必须匹配指定位置（通配符的位置）的一个字符。

```cpp
SELECT cust_contact
FROM Customers
WHERE cust_contact LIKE '[JM]%'
ORDER BY cust_contact;
```

此语句的WHERE子句中的模式为‘[JM]%’。此搜索模式使用了两个不同的通配符。[JM]匹配任何以方括号中字母开头的联系人名，它也只能匹配单个字符。因此任何多余一个字符的名字都不匹配。[JM]之后的%通配符匹配第一个字符之后的任意数目的字符，返回所需的结果。

此通配符可以使用前缀“^”来否定。例如，下面的查询匹配不以J或M起头的任意联系人名（与前一个例子相反）：

```cpp
SELECT cust_contact
FROM Customers
WHERE cust_contact LIKE '[^JM]%'
ORDER BY cust_contact;
```

当然，也可以使用NOT操作符得出相同的结果。“^”的唯一优点是在使用多个WHERE子句时简化语法：

```cpp
SELECT cust_contact
FROM Customers
WHERE NOT cust_contact LIKE '[JM]%'
ORDER BY cust_contact;
```

#### 使用通配符的技巧

正如所见，SQL的通配符很有用。但这种功能是很有代价的，即：通配符搜索的处理一般比前面讨论的其他搜索所花时间更长。这里给出一些使用通配符要记住的技巧：

1.不要过过分使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。

2.在确实需要使用通配符时，除非绝对有必要，否则不要把它们用在搜索模式的开始处。把通配符置于搜索模式的开始处，搜索起来是最慢的。

3.仔细注意通配符的位置。如果放错地方，可能不会返回想要的数据。

总之，通配符是一种极重要和有用的搜索工具，以后我们经常会用到它。














