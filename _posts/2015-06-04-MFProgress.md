---
layout: post 
title: Progress控件应用
categories: MFC学习
description: Progress控件应用
keywords:  Progress, MFC
---

1.创建一个基于对话框的应用程序；

2.给对话框添加一个Progress，一个static，一个按钮控件：

![](/images/posts/MFC/33.png)

修改static的ID：

![](/images/posts/MFC/34.png)

3.给Progress控件添加变量。

4.双击按钮添加处理函数:

```cpp
void CProgressDlg::OnBnClickedButton1()
{
	// TODO: 在此添加控件通知处理程序代码
	int i = 0;
	CString str;
	while(i <=100 )
	{
		m_progress.SetPos(i);//设置进度条
		str.Format(L"%d",i);
		GetDlgItem(IDC_VALUE)->SetWindowText(str);//显示进度条进度
		i++;
		Sleep(50);//休眠50毫秒
	}
}
```

5.运行结果：

![](/images/posts/MFC/35.png)