---
layout: post
title: 对象的动态建立和释放
categories: C和C++基础
description: 对象的动态建立和释放
keywords: 对象，建立，释放
---

一般方法定义的对象是静态的，在程序运行过程中，对象所占的空间是不能随时释放的。但有时人们希望在需要用到对象时才建立对象，在不需要用该对象时就撤销它，释放它所占的内存空间以供别的数据使用。这样可提高内存空间的利用率。

用new运算符动态地分配内存，用delete运算符释放这些内存空间。这也适用于对象，可以用new运算符动态建立对象，用delete运算符撤销对象。如果已经定义了一个Box类，可以用下面的方法动态地建立一个对象：

```cpp
new Box;
```

编译系统开辟了一段内存空间，并在此内存空间中存放一个Box类对象，同时调用该类的构造函数，以使该对象初始化(如果已对构造函数赋予此功能的话)。但是此时用户还无法访问这个对象，因为这个对象既没有对象名，用户也不知道它的地址。这种对象称为无名对象，它确实是存在的，但它没有名字。

用new运算符动态地分配内存后，将返回一个指向新对象的指针的值，即所分配的内存空间的起始地址。用户可以获得这个地址，并通过这个地址来访问这个对象。需要定义一个指向本类的对象的指针变量来存放该地址。如：

```cpp
Box *pt;      //定义一个指向Box类对象的指针变量pt
pt=new Box;   //在pt中存放了新建对象的起始地址
```

在程序中就可以通过pt访问这个新建的对象。如：

```cpp
cout<<pt->height;      //输出该对象的height成员
cout<<pt->volume( );   //调用该对象的volume函数，计算并输出体积
```

C++还允许在执行new时，对新建立的对象进行初始化。如：

```cpp
Box *pt=new Box(12,15,18);
```

调用对象既可以通过对象名，也可以通过指针。用new建立的动态对象一般是不用对象名的，是通过指针访问的，它主要应用于动态的数据结构，如链表。访问链表中的结点，并不需要通过对象名，而是在上一个结点中存放下一个结点的地址，从而由上一个结点找到下一个结点，构成链接的关系。在执行new运算时，如果内存量不足，无法开辟所需的内存空间，目前大多数C++编译系统都使new返回一个0指针值。只要检测返回值是否为0，就可判断分配内存是否成功。ANSI C++标准提出，在执行new出现故障时，就“抛出”一个“异常”，用户可根据异常进行有关处理。但C++标准仍然允许在出现new故障时返回0指针值。当前，不同的编译系统对new故障的处理方法是不同的。

在不再需要使用由new建立的对象时，可以用delete运算符予以释放。如：

```cpp
delete pt; //释放pt指向的内存空间
```

这就撤销了pt指向的对象。此后程序不能再使用该对象。如果用一个指针变量pt先后指向不同的动态对象，应注意指针变量的当前指向，以免删错了对象。在执行delete运算符时，在释放内存空间之前，自动调用析构函数，完成有关善后清理工作。