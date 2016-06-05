---
layout: post 
title: RichEdit控件应用
categories: MFC学习
description: RichEdit控件应用
keywords:  RichEdit, MFC
---

1.创建一个基于对话框的应用程序；

2.添加一个Button控件和一个Rich Edit控件：

同时修改Rich Edit属性：

![](/images/posts/MFC/40.png)

3.在CBRichEditApp类中InitInstance()函数添加代码对Rich Edit控件进行初始化：

添加代码：

```cpp
BOOL CBRichEditApp::InitInstance()
{
	// 如果一个运行在 Windows XP 上的应用程序清单指定要
	// 使用 ComCtl32.dll 版本 6 或更高版本来启用可视化方式，
	//则需要 InitCommonControlsEx()。否则，将无法创建窗口。
	INITCOMMONCONTROLSEX InitCtrls;
	InitCtrls.dwSize = sizeof(InitCtrls);
	// 将它设置为包括所有要在应用程序中使用的
	// 公共控件类。
	InitCtrls.dwICC = ICC_WIN95_CLASSES;
	InitCommonControlsEx(&InitCtrls);

	CWinApp::InitInstance();

	AfxEnableControlContainer();
	//初始化Rich Edit控件
	::AfxInitRichEdit2();
	.
	.
	.
}
```

4.双击按钮添加事件处理函数:

```cpp
void CBRichEditDlg::OnBnClickedButton1()
{
	// TODO: 在此添加控件通知处理程序代码
	//文件过滤
	CString filter = _T("文本文件 (*.txt)|*.txt|所有文件(*.*)|*.*||");
	CFileDialog dlg(TRUE,NULL,NULL,NULL,filter,this);
	if(dlg.DoModal() == IDOK)         //显示打开对话框，如果按下确定键
	{
		CStdioFile file;
		file.Open(dlg.GetPathName(),CFile::modeRead);//打开文件
		CString txt;
		CString tmpStr;
		//从文件中读取数据
		while(file.ReadString(tmpStr))
		{
			//txt = txt + tmpStr + "\n";
		   //strcpy(txt,txt + tmpStr + "\n");
			txt += tmpStr +_T("\n") ;
		}
		//CString strmy = txt + "\n";
		GetDlgItem(IDC_RICHEDIT21)->SetWindowText(txt);
	}
}
```

5.运行结果：

![](/images/posts/MFC/41.png)