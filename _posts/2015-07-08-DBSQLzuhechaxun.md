---
layout: post
title: 组合查询
categories: 数据库开发
description: 组合查询
keywords: 数据库
---

多数SQL查询都只包含从一个或多个表中返回数据的单条SELECT语句。但是，SQL也允许执行多个查询（多条SELECT语句），并将结果作为单个查询结果集返回。这些组合查询通常称为并(union）或复合查询。

#### 创建组合查询

可用UNION操作符来组合数条SQL查询语句。利用UNION，可给出多条SELECT语句，将它们的结果组合成单个结果集。

##### 使用UNION

```cpp
SELECT cust_name,cust_contact,cust_email
FROM Customers
WHERE cust_state IN('IL','IN','MI');
```

![](/images/posts/DataBase/117.png)

```cpp
SELECT cust_name,cust_contact,cust_email
FROM Customers
WHERE cust_name='Fun4All';
```

![](/images/posts/DataBase/118.png)

组合以上两条语句：

```cpp
SELECT cust_name,cust_contact,cust_email
FROM Customers
WHERE cust_state IN('IL','IN','MI');
UNION
SELECT cust_name,cust_contact,cust_email
FROM Customers
WHERE cust_name='Fun4All';
```

![](/images/posts/DataBase/119.png)

其实可以使用or来得到相同的结果：

```cpp
SELECT cust_name,cust_contact,cust_email
FROM Customers
WHERE cust_state IN('IL','IN','MI')
or cust_name='Fun4All';
```

#### 对组合查询结果进行排序

```cpp
SELECT cust_name,cust_contact,cust_email
FROM Customers
WHERE cust_state IN('IL','IN','MI');
UNION
SELECT cust_name,cust_contact,cust_email
FROM Customers
WHERE cust_name='Fun4All';
ORDER BY cust_name,cust_contact;
```

![](/images/posts/DataBase/120.png)