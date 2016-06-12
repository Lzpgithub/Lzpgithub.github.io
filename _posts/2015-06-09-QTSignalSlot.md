---
layout: post 
title: 深入理解信号和槽
categories: QT学习
description: 深入理解信号和槽
keywords:  QT
---

信号和槽机制是Qt编程的基础。它可以让应用程序编程人员把这些互不了解的对象绑定在一起。

**槽和普通的C++成员函数几乎是一样的**，可以是虚函数，可以被重载，也可以是公有的、保护的或者私有的，并且也可以被其他C++成员函数直接调用，它们的参数可以是任意类型。唯一不同的是：**槽可以和信号连接在一起，每当发射这个信号的时候，就会自动地调用这个槽**。

connect()语句：

```cpp
connect(sender,SIGNAL(signal),receiver,SLOT(slot));
```

其中sender和receiver是指向QOject的指针，也就是对象指针，signal和slot是不带参数的函数名。实际上SIGNAL()宏和SLOT()宏会把它们的参数转换成相应的字符串。

比如：

```cpp
connect(button,SIGNAL(clicked()),this,SLOT(show()));
```

可以简单理解为**当点击button对象时，button对象会发出clicked信号，button对象就作为sender，this是指this指针，指的是本对象，本对象作为receiver，clicked信号和本对象中的show槽连接，就会执行show槽函数**。

#### 一个信号可以连接多个槽

```cpp
connect(slider,SIGNAL(valueChanged(int)),spinBox,SLOT(setValue(int)));

connect(slider,SIGNAL(valueChanged(int)),this,SLOT(updateStatusBarIndicator(int)));
```

在发射这个信号的时候，会以不确定的顺序一个接一个地调用这些槽。


#### 多个信号可以连接同一个槽

```cpp
connect(lcd,SIGNAL(overflow()),this,SLOT(handleMathError()));
connect(calculator,SIGNAL(divisionByZero()),this,SLOT(handleMathError()));
```

无论发射的是哪一个信号，都会调用这个槽。


#### 一个信号可以与另外一个信号相连接

```cpp
connect(lineEdit,SIGNAL(textChanged(const QString &)),this,SIGNAL(updateRecord(const QString &)));
```

当发射第一个信号时，也会发射第二个信号，除此之外，信号与信号之间的连接和信号与槽之间的连接时难以区分的。

#### 连接可以被移除

```cpp
disconnect(lcd,SIGNAL(overflow()),this,SLOT(handleMathError()));
```

这种情况较少用到，因为当删除对象时，Qt会自动移除和这个对象相关的所有连接。


#### 参数类型和个数要匹配

要把信号成功连接到槽或者链接到另外一个信号，它们的参数必须具有相同的顺序和相同的类型：

```cpp
connect(ftp,SIGNAL(rawCommandReply(int,const QString &)),this,SLOT(processReply(int,const QString &)));
```

但是有个例外：如果信号的参数比它连接的槽的参数多，那么多余的参数就会被简单的忽略掉，比如：

```cpp
connect(ftp,SIGNAL(rawCommandReply(int const QString &)),this,SLOT(checkErrorCode(int)));
```

如果参数类型不匹配，或者如果信号或槽不存在，则当应用程序使用调试模式构建后，Qt会在运行时发出警告。如果在信号和槽的名字中包含了参数名，Qt也会发出警告。