---
layout: post 
title: Check Box控件应用
categories: MFC学习
description: 对话框菜单栏的创建MFC
keywords:  CheckBox, MFC
---

1.创建一个基于对话框的应用程序；

2.给对话框添加两个Check Box控件，一个Static控件：

![](/images/posts/MFC/18.png)

给控件1添加变量:

![](/images/posts/MFC/19.png)

3.给控件check1添加消息处理函数：

```cpp
void CCheckBoxDlg::OnClickedCheck1()
{
	// TODO: 在此添加控件通知处理程序代码
	UpdateData(true);
	if(m_check1)             //如果check被选中
		GetDlgItem(IDC_STATIC)->SetWindowTextW(L"Check1 is checked");
	else                     //如果check没被选中
	    GetDlgItem(IDC_STATIC)->SetWindowTextW(L"Check1 isn't checked");
}
```

4.程序运行效果：

![](/images/posts/MFC/20.png)