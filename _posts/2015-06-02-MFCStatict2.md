---
layout: post
title: Static控件应用2
categories: MFC学习
description: 对话框菜单栏的创建MFC
keywords:  static, MFC
---

1.创建一个基于对话框的应用程序；

2.在对话框中添加4个static控件，两用于提示，两个用于显示日期，时间：

![](/images/posts/MFC/6.png)

同时修改日期static的ID和时间static的ID：

![](/images/posts/MFC/7.png)

3.改写CGetTimeDlg类中OnInitDialog()函数，获取当前时间，并将其显示到对话框界面。生成一个定时器，为及时更新时间做好准备：

在函数中添加相应的代码：

```cpp
BOOL CGetTimeDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();

	// 将“关于...”菜单项添加到系统菜单中。
	.
	.
	.
	.
	// TODO: 在此添加额外的初始化代码
	SYSTEMTIME sti;
	CString data,time;
	GetLocalTime(&sti);     //获取本地时间
	data.Format(L"%4d-%2d-%2d",sti.wYear,sti.wMonth,sti.wDay);   //将日期格式化为字符串
	time.Format(L"%d:%d:%d",sti.wHour,sti.wMinute,sti.wSecond);  //将时间格式化为字符串

	GetDlgItem(IDC_DATA)->SetWindowText(data);  //显示日期
	GetDlgItem(IDC_TIME)->SetWindowText(time);  //显示时间
	SetTimer(1,1000,NULL);                      //设置定时器，程序一经启动，即可开始计时

	return TRUE;  // 除非将焦点设置到控件，否则返回 TRUE
}
```

4.给对话框CGetTimeDlg添加定时器处理函数:

![](/images/posts/MFC/8.png)

同时在消息处理函数中添加相关代码：

```cpp
void CGetTimeDlg::OnTimer(UINT_PTR nIDEvent)
{
	// TODO: 在此添加消息处理程序代码和/或调用默认值
	SYSTEMTIME sti;
	CString data,time;
	GetLocalTime(&sti);//获取本地时间
	data.Format(L"%4d-%2d-%2d",sti.wYear,sti.wMonth,sti.wDay);//将日期格式化为字符串
	time.Format(L"%d:%d:%d",sti.wHour,sti.wMinute,sti.wSecond);//将时间格式化为字符串

	GetDlgItem(IDC_DATA)->SetWindowText(data);//显示日期
	GetDlgItem(IDC_TIME)->SetWindowText(time);//显示时间

	CDialogEx::OnTimer(nIDEvent);
}
```

5.程序运行效果：

![](/images/posts/MFC/9.png)