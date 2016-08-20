---
layout: post
title: 创建和操作表
categories: 数据库开发
description: 创建和操作表
keywords: 数据库
---

#### 表创建基础

为利用CREATE TABLE创建表，必须给出下列信息：

1、新表的名字，在关键字CREATE TABLE之后给出；

2、表列的名字和定义，用逗号分隔；

3、有的DBMS还要求指定表的位置

创建Products表：

```cpp
CREATE TABLE Products(
	prod_id    CHAR(10)      NOT NULL,
	vend_id    CHAR(10)      NOT NULL,
	prod_price DECIMAL(8,2)  NOT NULL,
	prod_desc  VARCHAR(1000) NULL
);
```

NULL值就是没有值或缺值。允许NULL值得列也允许在插入行时不给出该列的值。不允许NULL值得列不接收该列没有值的行，换句话说，在插入或更新行是，该列必须有值。

创建混合了NULL和NOT NULL列的表：

```cpp
CREATE TABLE Vendors{
	vend_id       CHAR(10)   NOT NULL,
	vend_name     CHAR(50)   NOT NULL,
	vend_address  CHAR(50),
	vend_city     CHAR(50),
	vend_state    CHAR(5),
	vend_zip      CHAR(10),
	vend_country  CHAR(50)
};
```

#### 指定默认值

```cpp
CREATE TABLE OrderItems{
	order_num    INTEGER      NOT NULL,
	order_item   INTEGER      NOT NULL,
	prod_id      CHAR(10)     NOT NULL,
	quantity     INTEGER      NOT NULL    DEFAULT  1,
	item_price   DECIMAL(8,2) NOT NULL
};
```

#### 更新表

增加一列：

```cpp
ALTER TABLE Vendors
ADD vend_phone CHAR(20);
```
删除一列：

```cpp
ALTER TABLE Vendors
DROP COLUMN vend_phone;
```

#### 删除表

```cpp
DROP TABLE CustCopy
```

![](/images/posts/DataBase/120.png)