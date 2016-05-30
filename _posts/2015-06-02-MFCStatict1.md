---
layout: post
title: Static控件应用1
categories: MFC学习
description: 对话框菜单栏的创建MFC
keywords:  static, MFC
---

1.创建一个基于对话框的应用程序；

2.在对话框中添加Static控件和按钮控件：

![](/images/posts/MFC/3.png)

3.在对话框CShowSysTimeDlg类中添加消息计时函数：

![](/images/posts/MFC/4.png)

在函数中添加相应的代码：

```cpp
void CShowSysTimeDlg::OnTimer(UINT_PTR nIDEvent)
{
	// TODO: 在此添加消息处理程序代码和/或调用默认值
	SYSTEMTIME st;              //获取系统时间
	CString strDate,strTime;
	GetLocalTime(&st);
	strDate.Format(L"%4d-%2d-%2d",st.wYear,st.wMonth,st.wDay);
	strTime.Format(L"【%2d-%2d-%2d】",st.wHour,st.wMinute,st.wSecond);
	//将时间显示在Static text控件中
	GetDlgItem(IDC_STATIC)->SetWindowText(strDate+strTime);
	CDialogEx::OnTimer(nIDEvent);
}
```

4.双击按钮添加按钮点击处理函数：

```cpp
void CShowSysTimeDlg::OnBnClickedButton1()
{
	// TODO: 在此添加控件通知处理程序代码
	SetTimer(1,1000,0);                 //设置定时器，每秒进行更新一次
}
```

5.程序运行效果：

![](/images/posts/MFC/5.png)