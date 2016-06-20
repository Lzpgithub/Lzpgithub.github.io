---
layout: post 
title: QT Designer设计对话框
categories: QT学习
description: QT Designer设计对话框
keywords:  QT
---

Qt设计师(Qt Designer)为程序员提供了可供使用的新选择，它提供一种可视化的设计能力，可用于开发应用程序中的所有或部分窗体。

无论是使用手工编码还是使用Qt设计师，在创建对话框总是要包含以下这几个相同基本步骤：

1.创建并初始化子窗口部件；

2.把子窗口部件放到布局中；

3.设置Tab键顺序；

4.建立信号-槽之间的连接；

5.实现对话框中自定义槽；

#### VS2010创建QT工程

1.创建项目，选择Qt Application：

![](/images/posts/QT/2.png)

2.点击下一步：

![](/images/posts/QT/10.png)

3.点击下一步：

![](/images/posts/QT/11.png)


4.填写文件名和类名，点击下一步：

![](/images/posts/QT/12.png)

在这个页面中可以修改文件名，也可以**选择生成主窗口的类型QMainWindow、QDialog或者是QWidget**。

5.双击绿色文件：

![](/images/posts/QT/13.png)

6.打开Qt设计大师界面：

![](/images/posts/QT/14.png)

7.点击新建，可建立QMainWindow、QDialog、Qwidget窗体：

![](/images/posts/QT/29.png)


#### 添加窗口部件并修改相关属性

第一步是创建子窗口部件并且把它们放置到窗体中。创建一个标签、一个行编辑器、一个水平分隔符和两个按钮。从Qt设计师的窗口部件工具箱中拖拽其名字或者图标放到窗体中大概位置。在Qt设计师中，分隔符会显示为一个蓝色的弹簧，但在最终结果的窗体中它是不可见的。如图所示：

![](/images/posts/QT/15.png)

使用Qt设计师的属性编辑器可以设置每一个窗口部件的属性：

1.单击文本标签。修改此时ojectName的属性是“Label”，那么就可以将它的text属性设置成“&Cell Location”。

![](/images/posts/QT/16.png)

2.单击行编辑器。修改objectName属性是“lineEdit”。

![](/images/posts/QT/17.png)

3.单击第一个按钮。将它的objectName属性设置为“okButton”，将它的enabled属性设置为“false”，将它的text属性设置成“OK”，并且把它的default属性设置成“true”。

![](/images/posts/QT/18.png)

4.单击第二个按钮。将它的objectName属性设置成“cancelButton”，并且将它的text属性设置成“Cancel”

![](/images/posts/QT/19.png)

5.单击这个窗体中的空白地方，选中窗体本身。将objectName属性设置成“GoToCellDialog”，并且将他的windowTitle属性设置成“Go to Cell”。

![](/images/posts/QT/20.png)

![](/images/posts/QT/21.png)

到这一步，文本标签仍显示为“&Cell Location”。单击Edit->编辑伙伴 进入一种允许设置窗口部件伙伴(buddy)的特殊模式：

![](/images/posts/QT/22.png)

或者点击如下图标：

![](/images/posts/QT/23.png)

然后，单击这个标签并把红色箭头拖到行编辑器上，释放鼠标按键。现在，标签就会把行编辑器看成自己的伙伴。

![](/images/posts/QT/24.png)


调整窗口部件的相对位置：

1.单击“Cell Location”标签并且当单击与之相邻的行编辑器时按下Shift键，这样就可以同时选择它们。然后点击如下按钮：

![](/images/posts/QT/25.png)

被选中的窗口部件就会水平对称。

2.单击分隔符，然后在单击窗体的OK按钮和Cancel按钮时，一直按下Ctrl键。同样点击水平布局控制按钮。

3.最后调整窗口大小。

![](/images/posts/QT/26.png)

4.单击如下按钮：

![](/images/posts/QT/27.png)

在每一个可以接受焦点的窗口部件上，都会出现一个但蓝色的矩形：

![](/images/posts/QT/28.png)

按照你所希望的接受焦点的顺序单击每一个窗口部件。

最后关闭Qt设计师对话框，保存相关文件即可。



#### 生成文件分析

在VS2010环境下自动生成ui_example.h文件，这个文件包含了类Ui_GoToCellDialog的定义：

```cpp
class Ui_GoToCellDialog
{
public:
    QWidget *centralWidget;                     //相关窗口部件的变量声明
    QSplitter *splitter;
    QLabel *label;
    QLineEdit *lineEdit;
    QSplitter *splitter_2;
    QPushButton *okButton;
    QPushButton *cancelButton;
    QMenuBar *menuBar;
    QToolBar *mainToolBar;
    QStatusBar *statusBar;

    void setupUi(QMainWindow *GoToCellDialog)   //初始化函数
    {
        if (GoToCellDialog->objectName().isEmpty())
            GoToCellDialog->setObjectName(QString::fromUtf8("GoToCellDialog"));
        GoToCellDialog->resize(284, 145);
        centralWidget = new QWidget(GoToCellDialog);
	.
	.
	.
```

可以看出，**该生成类没有任何基类，值声明了一些成员变量，它们存储着窗体中的子窗口部件和子布局，以及用于初始化窗体的setupUi()函数。可以创建一个QDialog(QMainWindow或者QWidget)对象指针，然后把它传递给setupUi()函数**。

在ui_example.h文件中同样包含如下代码：

```cpp
namespace Ui {
    class GoToCellDialog: public Ui_GoToCellDialog {};
} // namespace Ui
```

在这里声明了一个叫**Ui的命名空间，在Ui命名空间中定义了类GoToCellDialog，该类公有继承了Ui_GoToCellDialog**。


在创建QT的工程中自动生成了example.h和example.cpp文件。

example.h：

```cpp
#ifndef EXAMPLE_H
#define EXAMPLE_H

#include <QtGui/QMainWindow>
#include "ui_example.h"

class example : public QMainWindow
{
	Q_OBJECT

public:
	example(QWidget *parent = 0, Qt::WFlags flags = 0);
	~example();


private:
	Ui_GoToCellDialog ui;    //或者是Ui::GoToCellDialog ui;

public slots:
	void handle_textchange_func();
	void reject();
};
#endif // EXAMPLE_H
```

ifndef、define和endif防止多重包含example.h文件。在该文件中声明了一个类example，该类公有继承类QMainWindow，同时定义了一个**私有变量GoToCellDialog类的对象ui**。

example.cpp：

```cpp
#include "example.h"
#include <QtGui>
#include <QMessageBox>

example::example(QWidget *parent, Qt::WFlags flags)
	: QMainWindow(parent, flags)
{
	ui.setupUi(this);  //初始化窗体 把指针传递给this
	QRegExp regExp("[A-Za-z][1-9][0-9]{0,2}");   //设定行编辑框的输入格式
	ui.lineEdit->setValidator(new QRegExpValidator(regExp,this));
	
	//有时候connect连接不成功，可以把槽函数名改一下试一试
	connect(ui.okButton,SIGNAL(clicked()),this,SLOT(reject()));
	connect(ui.cancelButton,SIGNAL(clicked()),this,SLOT(reject()));
	connect(ui.lineEdit,SIGNAL(textChanged(const QString &)),this,SLOT(handle_textchange_func()));
}

void example::handle_textchange_func(){
	ui.okButton->setEnabled(ui.lineEdit->hasAcceptableInput());
}

void example::reject(){
	this->close();
}

example::~example()
{

}
```

在构造函数中，设置了一个检验器QRegExpValidator来限制输入的范围。让它带一个正则表达式“**[A-Za-z][1-9][0-9]{0,2}**”，它的意思是：允许一个大写或者小写的字母，后面跟着一个范围为1~9的数字，后面再跟0个、1个或2个0~9的数字。

通过把this传递给QRegExpValidator的构造函数，使它成为example对象的一个子对象。这样以后**不用担心有关删除QRegExpValidator的事情了：当删除它的父对象时，它也会被自动删除**。

Qt的父子对象机制是在QObject中实现的。当利用一个父对象创建一个子对象(一个窗口部件，一个检验器，或是任意的其他类型)时，父对象会把这个子对象添加到自己的子对象列表中。**当删除这个父对象时，它会遍历子对象列表并且删除每个子对象，然后这些子对象再去删除它们自己所包含的每个子对象**。如此反复递归调用，直至清空所有子对象为止。这种父子对象机制可在很大程度上**简化内存管理工作，降低内存泄漏的风险**。需要明确删除的对象是那些使用new创建的并且没有父对象的对象。如果在删除一个父对象之前先删除了它的子对象，Qt会自动地从它的父对象的子对象列表中将其移除。

对于窗口部件，父对象还有另一层含义：**子窗口部件会显示在它的父对象所在的区域中。当删除这个父窗口部件时，不仅子对象会从内存中消失，而且它也会在屏幕上消失**。

上面是通过定义了一个私有变量GoToCellDialog类的对象ui来加载QT设计师的界面，这里也可以通过继承Ui_GoToCellDialog类来加载界面：

example.h：

```cpp
#ifndef EXAMPLE_H
#define EXAMPLE_H

#include <QtGui/QMainWindow>
#include "ui_example.h"

class example : public QMainWindow,public Ui_GoToCellDialog  //继承Ui_GoToCellDialog
{
	Q_OBJECT

public:
	example(QWidget *parent = 0, Qt::WFlags flags = 0);
	~example();

public slots:
	void handle_textchange_func();
	void reject();
};

#endif // EXAMPLE_H
```

example.cpp：

```cpp
#include "example.h"
#include <QtGui>
#include <QMessageBox>

example::example(QWidget *parent, Qt::WFlags flags)
	: QMainWindow(parent, flags)
{
	setupUi(this);  //初始化窗体 把指针传递给this
	QRegExp regExp("[A-Za-z][1-9][0-9]{0,2}");   //设定行编辑框的输入格式
	lineEdit->setValidator(new QRegExpValidator(regExp,this));
	
	connect(okButton,SIGNAL(clicked()),this,SLOT(reject()));
	connect(cancelButton,SIGNAL(clicked()),this,SLOT(reject()));
	connect(lineEdit,SIGNAL(textChanged(const QString &)),this,SLOT(handle_textchange_func()));
}

void example::handle_textchange_func(){
	okButton->setEnabled(lineEdit->hasAcceptableInput());
}

void example::reject(){
	this->close();
}

example::~example()
{

}
```

程序运行效果：

![](/images/posts/QT/30.png)







