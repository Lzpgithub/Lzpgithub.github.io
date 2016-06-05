---
layout: post 
title: Data Time Picker控件应用
categories: MFC学习
description: Data Time Picker控件应用
keywords:  Data Time Picker, MFC
---

1.创建一个基于对话框的应用程序；

2.添加一个Data Time Picker控件，一个static控件，一个按钮控件：


![](/images/posts/MFC/24.png)

给Data Time Picker控件添加变量：

![](/images/posts/MFC/25.png)

3.在CDataTimePickerDlg类中的OnInitDialog()函数中添加代码：

```cpp
BOOL CFontSizeDlg::OnInitDialog()
{
	CDialogEx::OnInitDialog();
	.
	.
	.
	// TODO: 在此添加额外的初始化代码
	SYSTEMTIME sysTime;
	::GetSystemTime(&sysTime);   //获取系统当前时间
	CTime time(sysTime);
	CTime timebegin(time.GetYear(),time.GetMonth(),time.GetDay(),
		time.GetHour(),time.GetMinute(),time.GetSecond());
	m_CTime.SetFormat(L"yyyy-MM-dd    HH:mm:ss");
	//显示系统时间
	((CDateTimeCtrl*) GetDlgItem(IDC_DATETIMEPICKER1))->SetTime(&timebegin);
	return TRUE;                 // 除非将焦点设置到控件，否则返回 TRUE
}
```

4.双击按钮添加处理函数：

```cpp
void CDataTimePickerDlg::OnBnClickedButton1()
{
	// TODO: 在此添加控件通知处理程序代码
	UpdateData(true);
	CString str;
	CTime m_Time;
	str.Format(L"%4d-%2d-%2d   %4d:%2d:%2d",
		m_Time.GetYear(),m_Time.GetMonth(),m_Time.GetDay(),
		m_Time.GetHour(),m_Time.GetMinute(),m_Time.GetSecond());
	GetDlgItem(IDC_STATIC)->SetWindowTextW(str);

}
```

5.程序运行效果：

![](/images/posts/MFC/26.png)

