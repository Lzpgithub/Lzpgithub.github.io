---
layout: post 
title: RadioButton控件应用
categories: MFC学习
description: RadioButton控件应用
keywords:  RadioButton, MFC
---

1.创建一个基于对话框的应用程序；

2.在对话框中添加3个RadioButton控件：

![](/images/posts/MFC/36.png)

给CRadioButton类添加背景颜色变量：

![](/images/posts/MFC/37.png)

3.给Radio Button1添加事件消息处理函数：

![](/images/posts/MFC/38.png)

添加代码：

```cpp
void CRadioButtonDlg::OnRadio1()
{
	// TODO: 在此添加命令处理程序代码
	m_BKColor = RGB(255,0,0);//背景红色
	Invalidate();

}
```

4.给Radio Button2添加事件消息处理函数

```cpp
void CRadioButtonDlg::OnRadio2()
{
	// TODO: 在此添加命令处理程序代码
	m_BKColor = RGB(0,255,0);//背景绿色
	Invalidate();
}
```

5.给Radio Button3添加事件消息处理函数:

```cpp
void CRadioButtonDlg::OnRadio3()
{
	// TODO: 在此添加命令处理程序代码
	m_BKColor = RGB(0,0,255);//背景蓝色
	Invalidate();
}
```

6.改写CRadioButtonDlg中的Onpaint()函数，改变背景颜色：

```cpp
void CRadioButtonDlg::OnPaint()
{

	CRect rect;
	CPaintDC dc(this);
	GetClientRect(rect);
	dc.FillSolidRect(rect,m_BKColor);//设置窗体背景颜色
	.
	.
	.
}
```

7.运行结果：

![](/images/posts/MFC/39.png)