---
layout: post 
title: ListBox控件应用
categories: MFC学习
description: ListBox控件应用
keywords:  ListBox, MFC
---

1.创建一个基于对话框的应用程序；

2.在对话框添加一个static控件，一个list box控件，一个按钮控件：

![](/images/posts/MFC/27.png)

给List Box控件添加变量：

![](/images/posts/MFC/28.png)

3.双击添加按钮处理函数：

```cpp
void CDiskListDlg::OnBnClickedButton1()
{
	// TODO: 在此添加控件通知处理程序代码
	m_listBox.ResetContent();//情况列表
	char drives[128];//存储所以驱动器名称
	char* pDrive;//驱动器指针
	//取得系统的第一个逻辑驱动器
	/*LPWSTR aaa = CA2W(drives);
	if(!GetLogicalDriveStrings(sizeof(drives),drives))
	{
		MessageBox(L"获取驱动器失败！");
		return;
	}*/
	pDrive = drives;//指向第一个逻辑驱动器
	//将驱动器字符放入列表中
	while(*pDrive)
	{
		//LPWSTR str = CA2W(pDrive);
		CString str(drives);
		m_listBox.AddString(_T("背景"));//将驱动器名称加入列表中
		pDrive += strlen(pDrive) + 1;
		
	}
}

```

4.程序运行效果：

![](/images/posts/MFC/29.png)

