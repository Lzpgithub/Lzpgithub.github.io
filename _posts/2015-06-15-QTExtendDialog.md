---
layout: post 
title: QT实现扩展对话框
categories: QT学习
description: QT实现扩展对话框
keywords:  QT，扩展对话框
---

扩展对话框是一种可以改变形状的对话框。

#### 扩展对话框的定义

扩展对话框通常只显示简单的外观，但是它还有一个切换按钮(toggle button)，可以让用户在对话框的简单外观和扩展外观之间来回切换。扩展对话框通常用于试图同时满足普通用户和高级用户需要的应用程序中，这种应用程序通常会隐藏那些高级选项，除非用户明确要求看到它们。

本节实验使用QT设计师创建电子制表软件应用程序的排序对话框(sort对话框)，在这个对话框中，用户可以选择一列或者多列进行排列。

#### 设计对话框

##### 设计Primary框

1.创建VS2010 QT项目，项目命名为SortDialog，生成的主窗口为QDialog类型；

2.打开QT设计师，创建OK按钮并把它拖放在窗体的右上角。将它的objectName修改为“okButton”，并将它额default属性设置为“true”；

3.创建Cancel按钮并把它拖放到OK按钮的下方。将它的objectName修改为“cancelButton”；

4.创建一个垂直分割符并把它拖放到Cancel按钮下方。然后创建一个More按钮，并将其放在垂直分隔符的下方。将More按钮的objectName修改为“moreButton”，text属性设置成“&More”，checkable属性设置为“true”。

5.单击OK按钮，按下Ctrl键后再单击Cancel按钮、垂直分隔符和More按钮，然后单击Form->Lay Out Vertically；

6.创建一个群组框、两个标签、两个组合框以及一个水平分隔符，然后把它们放到窗体上的任意位置。

7.拖动群组框的右下角使它变大，然后把其他窗口部件移动群组框中，放置到适当的位置中。

![](/images/posts/QT/31.png)

8.拖动第二个组合框的右边缘，使它的宽度大约为第一个组合框的两倍。

9.将群组框的title属性设置为“&Primary Key”，第一个标签的text属性设置为“Column：”，第二个标签的text属性设置为“Order：”

10.右键单击第一个组合框，从Qt设计师弹出的上下文菜单的组合框编辑器选择EditItems。用文本“None”创建一个项。

11.右键单击第二个组合框并且同样选择Edit Items。创建一个“Ascending”项和一个“Descending”项。

12.单击群组框，按住Ctrl键，再逐一单击群组框内的空间，然后单击Form->Lay Out in a Grid设定栅格布局。再次单击群组框，并且单击Form->Ajust size。

![](/images/posts/QT/32.png)


##### 设计Secondary框和Tertiary框

现在来添加其他两个群组框：Secondary Key和Tertiary Key：

1.让对话框窗口足够高，以便可以容纳另外两部分。

2.按下Ctrl键，然后单击并拖动Primary Key群组框，这样就可以在原群组框(以及它所包含的所有组件)的上方复制出一个新的群组框。把复制的这个群组框拖动到原群组框的下方。重复以上步骤，就可以生成第三个群组框，然后把它拖动到第二个群组框的下方。

3.将它们的title属性分别修改为“&Secondary Key”和“&Tertiary Key”。

4.创建一个垂直分隔符，并且把它放在Primary Key群组框和Secondary Key群组框的中间。

5.单击窗体，取消对任意选中窗体部件选择，然后单击Form->Lay Out in a Grid。现在向上和向左拖动窗体的右下角，以便让窗体尽可能地小。

![](/images/posts/QT/33.png)

6.把两个垂直分隔符的sizeHint属性设置为[20,0]。

最终的网格布局时4行和2列，一共有8个单元格。Primary Key群组框、最左边的垂直分隔符、Secondary Key群组框和Tertiary Key群组框各占一个单元的单元格。包含OK、Cancel和More按钮的垂直布局占用了两个单元格。最后，会在对话框的右下角剩下两个空白单元格。

然后把这个窗体重命名为“SortDialog”，并且把它的标题修改为“Sort”，然后根据下图修改各个子窗口部件的名称。

![](/images/posts/QT/34.png)

单击Edit->Edit Tab Order，从窗体的最上面到最下面依次单击每个组合框，然后单击窗体右侧的OK、Cancel和More按钮，用来设置焦点顺序。

![](/images/posts/QT/35.png)


##### 信号和槽的连接

现在窗体已经设计完成，可以开始着手设置一些信号-槽的连接来实现窗体的功能了。Qt设计师运行我们在构成同一窗体的不同部分内的窗口部件之间建立连接。我们需要两个连接。

单击Edit->Edit Signals/Slots，进入Qt设计师的设置连接模式。窗体中各个窗口部件之间的连接用蓝色箭头表示。**要在两个窗口部件之间建立连接，可以单击作为发射器的窗口部件并且拖动所产生的红色箭头线到作为接收器窗口部件上，然后松开鼠标**。这时会弹出一个对话框，可以从中选择建立连接的信号和槽。

要建立的第一个连接位于okButton按钮和窗体的accept()槽之间。把从okButton按钮开始的红色箭头线拖动到窗体的空白区域，然后松开按键，如此便会弹出如图所示的设置连接对话框：

![](/images/posts/QT/36.png)

从该对话框中选择clicked()作为信号，选择accept()作为槽，然后单击OK按钮。

对于第二个连接，把从cancelButton按钮开始的红色箭头线拖动到窗体的空白区域，然后在设置连接对话框中连接按钮的clicked()信号和窗体的reject()槽。

要建立的第三个连接位于moreButton按钮和secondaryGroupBox群组框之间。在这两个窗口部件之间拖动红色箭头线，然后选择toggled(bool)作为信号，选择setVisible(bool)作为槽。默认情况下，setVisible(bool)槽不会显示在Qt设计师的槽列表中，但如果选中了“Show all signals and slots”选项，就可以看到这个槽了：

![](/images/posts/QT/37.png)

第四个要建立连接是moreButton按钮的toggled(bool)作为信号和tertiaryGroupBox群组框的setVisible(bool)槽之间的连接。

至此对话框的相关信号和槽已经连接完成。

##### 编写C++文件

sortdialog.h：

```cpp
#ifndef SORTDIALOG_H
#define SORTDIALOG_H

#include <QtGui/QDialog>
#include "ui_sortdialog.h"

class SortDialog : public QDialog
{
	Q_OBJECT

public:
	SortDialog(QWidget *parent = 0, Qt::WFlags flags = 0);
	~SortDialog();

	void setColumnRange(QChar first,QChar last);

private:
	Ui::SortDialogClass ui;
};

#endif // SORTDIALOG_H
```

sortdialog.cpp：

```cpp
#include "sortdialog.h"

SortDialog::SortDialog(QWidget *parent, Qt::WFlags flags)
	: QDialog(parent, flags)
{
	ui.setupUi(this);

	ui.secondaryGroupBox->hide();   //首先将secondary群组框和tertiary群组框隐藏
	ui.tertiaryGroupBox->hide();
	layout()->setSizeConstraint(QLayout::SetFixedSize);
	setColumnRange('A','Z');

}

SortDialog::~SortDialog()
{

}

void SortDialog::setColumnRange(QChar first,QChar last){
	ui.primaryColumnCombo->clear();
	ui.secondaryColumnCombo->clear();
	ui.tertiaryColumnCombo->clear();

	ui.secondaryColumnCombo->addItem(tr("None"));
	ui.tertiaryColumnCombo->addItem(tr("None"));
	ui.primaryColumnCombo->setMinimumSize(ui.secondaryColumnCombo->sizeHint());

	QChar ch=first;
	while(ch<=last){
		ui.primaryColumnCombo->addItem(QString(ch));
		ui.secondaryColumnCombo->addItem(QString(ch));
		ui.tertiaryColumnCombo->addItem(QString(ch));
		ch=ch.unicode()+1;
	}
}
```

构造函数隐藏了对话框的第二键和第三键这两个部分。它也**把有关窗体布局的sizeConstraint属性设置为QLayout::SetFixedSize，这样会使用户不能重新修改这个对话框窗体的大小。这样一来，布局就会负责对话框重新定义大小的职责，并且也会在显示和隐藏子窗口部件的时候自动重新定义这个对话框的大小，从而可以确保对话框总是能以最佳的尺寸显示出来**。

QWidget：：sizeHint()函数可以返回布局系统试图认同的“理想”大小。这也解释了为什么不同的窗口部件或者具有不同内容的类似窗口部件通常会被布局系统分配给不同的尺度大小。

对于第二键组合框和第三键组合框，由于它们包含了一个“None”选项，所以它们要比只包含了一个单字符项目的主键组合框显得宽一些。为了避免这种不一致性，使用setMinimumSize函数把主键组合框的最小的大小设置成第二键组合框的理想大小。

在实际的应用程序中，控制扩展对话框的按钮通常会在只显示了基本对话框时显示为“Advanced<<<”，而在显示了扩展对话框时才显示为“Advanced<<<”。这在Qt中实现起来非常容易，只需在单击这个按钮时调用QPushButton中的setText()函数即可完成这一功能。

程序运行结果：

![](/images/posts/QT/38.png)  ![](/images/posts/QT/39.png)

