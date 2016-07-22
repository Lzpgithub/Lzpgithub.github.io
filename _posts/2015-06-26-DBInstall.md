---
layout: post
title: SQL Server 2005数据库的安装
categories: 数据库开发
description: SQL Server 2005数据库的安装
keywords: 数据库
---

SQL Server 是Microsoft 公司推出的关系型数据库管理系统。具有使用方便可伸缩性好与相关软件集成程度高等优点，可跨越从运行Microsoft Windows 98的膝上型电脑到运行Microsoft Windows 2012的大型多处理器的服务器等多种平台使用。本实验以 SQL Server 2005为例介绍数据库的安装过程。

#### 软件安装

首先下载SQL Server 2005数据库软件：

![](/images/posts/DataBase/40.png) 

在次之前还要解决IIS功能要求警告的解决，只需一步一步按以下操作即可：

![](/images/posts/DataBase/41.png) 

![](/images/posts/DataBase/42.png) 

![](/images/posts/DataBase/43.png) 

![](/images/posts/DataBase/44.png) 

![](/images/posts/DataBase/45.png) 

![](/images/posts/DataBase/46.png) 

![](/images/posts/DataBase/47.png) 

![](/images/posts/DataBase/48.png) 


然后使用虚拟软件打开映像，根据安装向导一步一步地安装：

![](/images/posts/DataBase/49.png) 

![](/images/posts/DataBase/50.png) 

![](/images/posts/DataBase/51.png) 

![](/images/posts/DataBase/52.png) 

![](/images/posts/DataBase/53.png) 

![](/images/posts/DataBase/54.png) 

在这一步，一定要记住登录数据库的密码，因为以后要靠这个密码登录数据库，登录名默认是sa。

![](/images/posts/DataBase/55.png) 

安装完成以后可以在开始->程序中看到：

![](/images/posts/DataBase/56.png) 

至此数据库软件安装完成。

#### 数据源的配置

数据安装完成后还不能使用，因为数据库软件还不知道以哪个机器作为服务器和数据源，因此必须配置数据源。我们可以把本地计算机或远程计算机配置为数据源。本实验把本机配置为数据源，因此数据库软件会访问本地的数据库。

配置数据源步骤：

![](/images/posts/DataBase/57.png) 

![](/images/posts/DataBase/58.png) 

![](/images/posts/DataBase/59.png) 

![](/images/posts/DataBase/60.png) 

![](/images/posts/DataBase/61.png) 

在这一步中，选择服务器的下拉列表框中一定要选择本地的电脑的用户，使本地电脑作为服务器；

![](/images/posts/DataBase/62.png) 

这一步要登陆数据库，登录名是sa，密码就是前面设置的数据密码。

![](/images/posts/DataBase/63.png) 

![](/images/posts/DataBase/64.png) 

至此数据源设置完成。

#### 创建数据库和表

打开数据库软件，然后登陆数据库：

![](/images/posts/DataBase/65.png) 

右键点击创建数据库DataBase：

![](/images/posts/DataBase/66.png) 

如图得到一个数据库DataBase

![](/images/posts/DataBase/67.png) 


点击新建查询创建一个数据库代码编辑文件：

![](/images/posts/DataBase/68.png) 

并添加代码：

```cpp
CREATE TABLE Movies(
	title      CHAR(100),
	year       INT,
	length     INT,
	genre      CHAR(10),
	studioName CHAR(30),
	producerC# INT
);
```

同时要记得切换数据库：

![](/images/posts/DataBase/69.png) 

选中代码，点击执行：

![](/images/posts/DataBase/70.png) 

我们看到新创建的表：

![](/images/posts/DataBase/71.png) 

右击打开表：

![](/images/posts/DataBase/72.png) 

#### 启动和断开数据库服务

在管理工具中打开服务：

![](/images/posts/DataBase/73.png) 

弹出对话框，选择列表框中的条目按s键找到SQL相关服务，右击把属性都设置为手动启动，其中有一项叫SQL Server (MSSQLSERVER)，在连接数据库前要手动将其启动，否则无法连接数据库。当不使用数据库时应将其停止，应为该程序比较占用资源。

![](/images/posts/DataBase/74.png) 